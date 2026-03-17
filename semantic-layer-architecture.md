# Semantic Layer Architecture

## Purpose

This document is a full reconstruction specification for a custom Salesforce semantic layer. It is written so that a competent Salesforce engineer or AI agent can rebuild the system from scratch using only this document, with no access to the original codebase.

The system solves a specific problem: large Salesforce orgs with complex managed package object models have no shared vocabulary for what their data *means*. Object and field API names are implementation details. The semantic layer is a registry that maps those physical names to business concepts, defines how those concepts connect, and provides a query engine that traverses those connections into flat, consumable result sets — without any compile-time dependency on the underlying objects.

---

## Core Design Principles

These decisions are locked. Deviating from any of them breaks the system's fundamental contract.

### 1. CMDT-only storage
All registry configuration lives in Custom Metadata Types. No custom objects, no custom settings, no static resources. Rationale: CMDTs are packageable, deployable via Metadata API, cacheable at zero SOQL cost, and subscriber-overridable where appropriate. They travel with the package.

### 2. String-based schema references
Every reference to an SObject or field is stored as an API name string (e.g. `FF__Risk__c`, `FF__Risk_Score__c`). There are no compile-time schema tokens (`Schema.FF__Risk__c`). Rationale: the system must work across multiple managed package namespaces, unmanaged customer objects, and orgs where specific objects may not exist. String references degrade gracefully; compile-time references throw deploy errors.

### 3. Zero package dependencies
The service layer has no compile-time imports of classes from any managed package. It uses only standard Apex, standard `Schema.*` APIs, and its own classes. Rationale: this system may sit on top of multiple packages simultaneously; any managed package dependency would create version entanglement.

### 4. Graceful degradation, never hard failure
When a referenced object is missing from the org, a field is inaccessible due to FLS, or SOQL limits are approaching, the system emits a warning and continues with reduced results. It does not throw exceptions at the caller. The caller always gets a result set and a metadata object — the metadata object carries any warnings. The only exception: if the *root* hop of a traversal is inaccessible, there is nothing to return, and that is a legitimate hard failure.

### 5. Security enforcement per hop
Field-level security and CRUD access are checked for every hop independently before any SOQL is issued. Fields the running user cannot read are stripped from the SELECT clause and excluded from output. Objects the user cannot read are skipped. This happens transparently — the result set simply omits what the user cannot see, with a warning recorded.

---

## CMDT Data Model

Seven CMDT types form the complete registry. The DeveloperName of each record is its stable identifier — all cross-references use DeveloperName, never record Id.

### Type 1: Entity

**Purpose:** Maps a business concept to a physical Salesforce SObject. An entity is the fundamental node in the semantic graph.

| Field | API Name | Type | Size | Manageability | Purpose |
|-------|----------|------|------|---------------|---------|
| Active | `Active__c` | Checkbox | — | SubscriberControlled | Soft-disable without deletion. All queries filter on this. |
| SObject API Name | `SObject_API_Name__c` | Text | 255 | DeveloperControlled | Physical SObject (e.g. `Account`, `FF__Risk__c`). Must be a valid API name in the target org. |
| Description | `Description__c` | LongTextArea | 2000 | DeveloperControlled | What this entity means in the business domain. Used to build semantic context for LLM consumers. |
| Synonyms | `Synonyms__c` | LongTextArea | 1000 | SubscriberControlled | Comma-separated aliases. Used for natural language matching and LLM context. Subscriber-overridable so customers can add their own terminology. |
| Category | `Category__c` | Picklist | — | DeveloperControlled | Logical grouping (e.g. `Core_Asset`, `Risk`, `Compliance`, `Operational`). Used for filtering and UI organisation. |
| Primary Name Field | `Primary_Name_Field__c` | Text | 255 | DeveloperControlled | The display name field for this object (defaults to `Name`). Used when the object has a non-standard name field. |
| Default Filter | `Default_Filter__c` | LongTextArea | 2000 | SubscriberControlled | Optional SOQL WHERE fragment applied to every query on this entity (e.g. `IsActive = true`). Injected by the query executor at runtime. |

**DeveloperName convention:** Use the business concept, not the object name. `Account_Vendor` not `Account`. `Risk_Register_Entry` not `FF_Risk`. This allows multiple entity records to map to the same SObject for different semantic purposes.

---

### Type 2: Relationship

**Purpose:** Defines a directed edge between two entities. An edge is how the traversal engine moves from one node to another in the graph. The directionality is FROM a source entity TO a target entity, and the traversal type defines the SQL join pattern used.

| Field | API Name | Type | Size | Manageability | Purpose |
|-------|----------|------|------|---------------|---------|
| Active | `Active__c` | Checkbox | — | SubscriberControlled | Soft-disable. |
| From Entity | `From_Entity__c` | MetadataRelationship → Entity | — | DeveloperControlled | Source entity DeveloperName reference. |
| To Entity | `To_Entity__c` | MetadataRelationship → Entity | — | DeveloperControlled | Target entity DeveloperName reference. |
| Traversal Type | `Traversal_Type__c` | Picklist | — | DeveloperControlled | `Lookup_Up`, `Lookup_Down`, or `Junction`. Determines the query pattern (see traversal algorithm below). |
| Lookup Field API Name | `Lookup_Field_API_Name__c` | Text | 255 | DeveloperControlled | For Lookup_Up/Down: the API name of the lookup field that connects the two objects. |
| Junction Object API Name | `Junction_Object_API_Name__c` | Text | 255 | DeveloperControlled | For Junction only: the API name of the junction object (e.g. `FF__VendorRisk__c`). |
| Junction From Lookup | `Junction_From_Lookup__c` | Text | 255 | DeveloperControlled | For Junction: field on the junction object pointing back to the From entity. |
| Junction To Lookup | `Junction_To_Lookup__c` | Text | 255 | DeveloperControlled | For Junction: field on the junction object pointing to the To entity. |
| Semantic Label | `Semantic_Label__c` | Text | 255 | DeveloperControlled | Human-readable description of this connection (e.g. `"mitigated by"`, `"owned by"`). Used in LLM context and UI display. |
| Description | `Description__c` | LongTextArea | 2000 | DeveloperControlled | Business meaning of this connection. |
| Cardinality | `Cardinality__c` | Picklist | — | DeveloperControlled | `One_to_One`, `One_to_Many`, `Many_to_One`, `Many_to_Many`. Informational — the traversal engine uses Traversal_Type, not Cardinality, for query logic. |
| Junction Filter | `Junction_Filter__c` | LongTextArea | 2000 | SubscriberControlled | Optional WHERE fragment applied to the junction object query. |

**Traversal type semantics:**

- `Lookup_Down`: The *target* object has a lookup field pointing to the *source* object. Query pattern: `SELECT [fields] FROM [target] WHERE [Lookup_Field_API_Name] IN :sourceIds`
- `Lookup_Up`: The *source* object has a lookup field pointing to the *target* object. The lookup values are collected from source records, then used to query the target. Query pattern: `SELECT [fields] FROM [target] WHERE Id IN :collectedLookupValues`
- `Junction`: A many-to-many relationship via an intermediate object. Two queries: (a) `SELECT [Junction_From_Lookup], [Junction_To_Lookup] FROM [Junction_Object] WHERE [Junction_From_Lookup] IN :sourceIds`, then (b) `SELECT [fields] FROM [target] WHERE Id IN :collectedTargetIds`

---

### Type 3: Semantic Field

**Purpose:** Defines a semantically meaningful field on an entity. Not all fields on an SObject are declared here — only those with a defined business meaning that should be exposed through the query layer.

| Field | API Name | Type | Size | Manageability | Purpose |
|-------|----------|------|------|---------------|---------|
| Active | `Active__c` | Checkbox | — | SubscriberControlled | Soft-disable. |
| Entity | `Entity__c` | MetadataRelationship → Entity | — | DeveloperControlled | Parent entity reference. |
| Field API Name | `Field_API_Name__c` | Text | 255 | DeveloperControlled | Physical field API name on the entity's SObject. |
| Logical Name | `Logical_Name__c` | Text | 255 | DeveloperControlled | Human-readable field name used in output column headers and semantic context. |
| Field Role | `Field_Role__c` | Picklist | — | DeveloperControlled | `Dimension` (categorical/groupable), `Metric` (aggregatable numeric), `Fact` (descriptive text/data), `Identifier` (record identity). Guides how consumers use the field. |
| Data Type | `Data_Type__c` | Picklist | — | DeveloperControlled | `Text`, `Number`, `Currency`, `Percent`, `Date`, `DateTime`, `Boolean`, `Picklist`, `Reference`. Logical type used by consumers — independent of Salesforce's internal type. |
| Aggregate Function | `Aggregate_Function__c` | Picklist | — | DeveloperControlled | `SUM`, `AVG`, `COUNT`, `MIN`, `MAX`. Applicable to Metric fields only. Indicates the intended aggregate operation for analytics consumers. |
| Synonyms | `Synonyms__c` | LongTextArea | 1000 | SubscriberControlled | Comma-separated aliases for NL matching. |
| Description | `Description__c` | LongTextArea | 2000 | DeveloperControlled | What this field means in the business domain. |
| Sort Order | `Sort_Order__c` | Number(4,0) | — | DeveloperControlled | Display order within entity. Used for column ordering in output. |

---

### Type 4: Semantic View

**Purpose:** A named, reusable traversal query — a pre-defined question that the system knows how to answer. A view has a root entity, an ordered sequence of relationship hops (defined in View Hop records), and a set of output columns (defined in View Field records).

| Field | API Name | Type | Size | Manageability | Purpose |
|-------|----------|------|------|---------------|---------|
| Active | `Active__c` | Checkbox | — | SubscriberControlled | Soft-disable. |
| Root Entity | `Root_Entity__c` | MetadataRelationship → Entity | — | DeveloperControlled | The entity where traversal begins. The root query is always executed first. |
| Description | `Description__c` | LongTextArea | 2000 | DeveloperControlled | What question this view answers in natural language. |
| Context Instructions | `Context_Instructions__c` | LongTextArea | 5000 | SubscriberControlled | Instructions passed to an LLM consumer when generating an answer from this view's data. Domain-specific. |
| Max Records | `Max_Records__c` | Number(5,0) | — | DeveloperControlled | Hard cap on result rows. Default 200. Prevents runaway Cartesian products. |
| Synonyms | `Synonyms__c` | LongTextArea | 1000 | SubscriberControlled | Alternative names and phrasings for this view. Used for NL matching and LLM context. |
| Category | `Category__c` | Picklist | — | DeveloperControlled | Logical grouping of views (e.g. `Risk`, `Compliance`, `Operational`). |

---

### Type 5: View Hop

**Purpose:** One step in a view's traversal path. A view with N hops executes N+1 queries (root + one per hop). Hops are ordered and each references a Relationship record, which carries the traversal type and physical field mappings.

| Field | API Name | Type | Size | Manageability | Purpose |
|-------|----------|------|------|---------------|---------|
| Semantic View | `Semantic_View__c` | MetadataRelationship → Semantic View | — | DeveloperControlled | Parent view. |
| Relationship | `Relationship__c` | MetadataRelationship → Relationship | — | DeveloperControlled | Which edge to traverse at this step. |
| Hop Order | `Hop_Order__c` | Number(3,0) | — | DeveloperControlled | 1-based execution sequence. Must be contiguous starting from 1. |
| Is Optional | `Is_Optional__c` | Checkbox | — | DeveloperControlled | If true, behaves as a LEFT JOIN — root records with no matching hop records are still included in output (with null values for hop fields). If false, root records with no match are excluded (INNER JOIN behaviour). |

---

### Type 6: View Field

**Purpose:** Declares which Semantic Field records appear as output columns in a given view. This is a many-to-many join between views and semantic fields, with an optional alias and column ordering.

| Field | API Name | Type | Size | Manageability | Purpose |
|-------|----------|------|------|---------------|---------|
| Semantic View | `Semantic_View__c` | MetadataRelationship → Semantic View | — | DeveloperControlled | Parent view. |
| Semantic Field | `Semantic_Field__c` | MetadataRelationship → Semantic Field | — | DeveloperControlled | Which field to include in this view's output. |
| Column Alias | `Column_Alias__c` | Text | 255 | DeveloperControlled | Output column name override. Falls back to the Semantic Field's `Logical_Name__c` if blank. |
| Sort Order | `Sort_Order__c` | Number(4,0) | — | DeveloperControlled | Column order in the output result set. |

---

### Type 7: Verified Query

**Purpose:** Gold-standard question-to-view mappings used to improve semantic view selection accuracy. These are training examples for LLM-based view selection and keyword fallback matching. They are operational metadata, not business data.

| Field | API Name | Type | Size | Manageability | Purpose |
|-------|----------|------|------|---------------|---------|
| Active | `Active__c` | Checkbox | — | SubscriberControlled | Soft-disable. |
| Semantic View | `Semantic_View__c` | MetadataRelationship → Semantic View | — | DeveloperControlled | The view this question maps to. |
| Question | `Question__c` | LongTextArea | 1000 | DeveloperControlled | A natural language question that this view can answer. |
| Answer Pattern | `Answer_Pattern__c` | LongTextArea | 3000 | DeveloperControlled | Gold-standard answer structure or exemplar response for this question. Used in answer generation prompts. |
| Category | `Category__c` | Picklist | — | DeveloperControlled | `View_Selection` (used to train view picker), `Answer_Quality` (used in answer generation prompt), `Both`. |
| Priority | `Priority__c` | Number(3,0) | — | DeveloperControlled | Sort order for inclusion in prompts when there are too many to include (1 = highest priority). |
| Synonyms | `Synonyms__c` | LongTextArea | 1000 | SubscriberControlled | Alternative phrasings of this question. Used in keyword fallback matching. |
| Business Context | `Business_Context__c` | LongTextArea | 2000 | SubscriberControlled | Why this question matters in the business domain. Injected into LLM prompts as context. |

---

## Service Layer Architecture

The service layer is a pipeline of independent, single-responsibility Apex classes. Each class has one job and knows nothing about any other class except its immediate input and output contracts. The orchestrating service composes them.

### Pipeline (in execution order)

```
executeView(viewDeveloperName)
    │
    ├─ 1. REGISTRY LOAD       — fetch all 7 CMDT types (0 SOQL, cached per transaction)
    │
    ├─ 2. SCHEMA VALIDATION   — verify all SObjects and fields referenced by the view
    │                           exist in the org's describe metadata (0 SOQL, cached)
    │
    ├─ 3. PATH RESOLUTION     — load the ordered hop list for the view; for each hop,
    │                           resolve the Relationship record and build the field list
    │                           after FLS filtering
    │
    ├─ 4. ROOT QUERY          — execute SOQL on the Root Entity (1 SOQL)
    │
    ├─ 5. HOP ITERATION       — for each hop in order:
    │       ├─ Lookup_Down:   1 SOQL
    │       ├─ Lookup_Up:     1 SOQL
    │       └─ Junction:      2 SOQL (junction first, then target)
    │
    ├─ 6. FLATTEN             — Cartesian product across all hops into
    │                           List<Map<String, Object>> (0 SOQL, in-memory)
    │
    └─ RETURN                 — result set + QueryMetadata
```

**SOQL budget:** A view with a root + N hops uses between N+1 (all Lookup) and 2N+1 (all Junction) queries. A 3-hop view costs 4–7 queries. Plan views accordingly against the 100-query governor limit.

### Registry Service

Loads all 7 CMDT types via SOQL on first access and caches them in static maps for the duration of the transaction. All subsequent access is in-memory. Because CMDT queries do not count against the org's SOQL governor limits, the initial load is free in that dimension but does count against heap — plan accordingly for large registries.

Provides accessor methods:
- `getAllEntities()` → `Map<String, Entity__mdt>`
- `getAllRelationships()` → `Map<String, Relationship__mdt>`
- `getAllViews()` → `Map<String, Semantic_View__mdt>`
- `getFieldsForEntity(entityDevName)` → `List<Semantic_Field__mdt>`
- `getHopsForView(viewDevName)` → `List<View_Hop__mdt>` (sorted by Hop_Order)
- `getFieldsForView(viewDevName)` → `List<View_Field__mdt>` (sorted by Sort_Order)

### Schema Validator

Wraps `Schema.getGlobalDescribe()` and per-object `DescribeSObjectResult.fields.getMap()`. Both are cached statically. Accepts string API names and returns whether the object/field exists and is accessible.

**Namespace handling:** The validator must try both the bare name and namespaced variants. Since the service layer has no compile-time knowledge of which namespace prefixes exist in a given org, it introspects the class's own namespace (via class name splitting) and applies it when looking up fields. When a bare name fails, retry with `[namespace]__[name]`.

### Security Engine

Receives a list of string field API names and an SObject type. Returns a filtered list containing only the fields the running user can read (`isAccessible()`). If the user lacks CRUD read on the object itself, returns an empty list and records a warning. Never throws — always returns a (possibly empty) filtered list.

### Path Resolver

Takes a view DeveloperName and produces an ordered list of resolved hops. Each resolved hop contains:
- The target entity's SObject API name
- The traversal type
- The physical lookup/junction field names
- The security-filtered list of fields to SELECT

This is the only class that combines registry lookups with security checks. Its output is a ready-to-execute query plan.

### Query Executor

Takes the resolved hop list and executes SOQL iteratively. Key behaviours:

- Checks `Limits.getQueries()` before each hop. If fewer than 5 queries remain (configurable buffer), stops, records a warning, and returns partial results.
- After each hop, stores results as `Map<Id, List<SObject>>` keyed by the parent FK value. This grouping is what the flattener uses to reconstruct join relationships.
- For Junction hops, executes two queries per hop: the junction object first (collecting FK pairs), then the target object.
- Empty hop results are stored as empty maps, not nulls. The flattener must distinguish between "this hop returned nothing" and "this hop was skipped".

### Result Flattener

Takes the root records plus the map-of-maps from each hop and produces `List<Map<String, Object>>`. The algorithm is a recursive Cartesian product:

```
for each root record:
    for each matching record in hop 1:
        for each matching record in hop 2:
            ... (recurse)
                emit one flat Map<String, Object> combining all fields
```

For optional hops (`Is_Optional = true`), if there are no matching records at that hop, emit one row with null values for all hop fields (LEFT JOIN behaviour). For required hops, emit nothing for that root record (INNER JOIN behaviour).

**Heap risk:** The Cartesian product can grow explosively if a root record has many children at multiple hops. For example, 100 root records × 50 hop-1 children × 20 hop-2 children = 100,000 rows. The `Max_Records__c` field on the view is a hard cap that truncates before returning. The flattener must also check heap usage during iteration and truncate with a warning if approaching the 12MB limit.

**Column naming:** Output map keys use the View Field's `Column_Alias__c` if set, otherwise the Semantic Field's `Logical_Name__c`. Column names are therefore stable and human-readable, independent of physical field API names.

### Orchestrating Service

The public entry point that composes all the above. Exposes:

- `executeView(viewDevName)` → full pipeline, returns result set + metadata
- `getAvailableViews()` → list of active view labels and DeveloperNames (used by UI pickers)
- `executeViewFromLWC(viewDevName)` → `@AuraEnabled` wrapper for `executeView`

Returns a structured result object containing:
- `List<Map<String, Object>> records` — the flat result set
- `List<String> columns` — ordered column names
- `QueryMetadata metadata` — totalRecords, totalQueries, hopCount, executionTimeMs, warnings, per-hop query step details

---

## Query Metadata

Every call returns metadata alongside data. The metadata object contains:

| Field | Type | Purpose |
|-------|------|---------|
| `totalRecords` | Integer | Number of rows in the result set |
| `totalQueries` | Integer | Number of SOQL queries consumed |
| `hopCount` | Integer | Number of hops traversed |
| `executionTimeMs` | Long | Wall-clock time from start to flatten complete |
| `truncated` | Boolean | True if results were capped by Max_Records or heap |
| `warnings` | List\<String\> | Human-readable degradation notices |
| `querySteps` | List\<QueryStep\> | Per-hop details: label, SObject name, traversal type, record count, field count, whether it was skipped |

Consumers must treat `warnings` as first-class output. A non-empty warnings list means the result set is incomplete or filtered.

---

## Known Limitations

### 1. Cartesian product blowup
The in-memory flattener has no streaming or pagination. For wide, multi-hop views over large data sets, the heap fills before the query limit is hit. The current mitigation (Max_Records + heap check) truncates results but provides no way to page through a large result set.

**Future state:** Introduce cursor-based pagination at the executor level. Rather than collecting all hop results in memory, stream and flatten incrementally, yielding chunks of rows.

### 2. No real-time schema invalidation
The schema validator caches `Schema.getGlobalDescribe()` statically per transaction. If an org's schema changes between transactions (field added, object deactivated), the cache is automatically refreshed on the next transaction. However, within a long-running transaction that makes multiple view calls, stale cache could cause the validator to believe a field is accessible when it no longer is.

**Future state:** This is an acceptable tradeoff for the current governor model. No action needed unless the system is used in batch contexts.

### 3. WHERE clause injection is unvalidated
The `Default_Filter__c` on Entity and `Junction_Filter__c` on Relationship are injected directly into SOQL strings. There is no parsing or validation of these fragments before injection.

**Future state:** Add a SOQL fragment validator that parses and whitelists safe WHERE expressions. Until then, these fields should only be set by trusted developers, not exposed to end users.

### 4. No aggregate query support
The `Aggregate_Function__c` field on Semantic Field is metadata only — the query executor always runs row-level queries. There is no mechanism to execute `SELECT SUM(FF__Risk_Score__c)` style aggregate SOQL through the current pipeline.

**Future state:** A separate aggregate executor that builds GROUP BY queries from Metric fields. Views would need an `Aggregation_Mode__c` flag to distinguish row-level from aggregate execution.

### 5. No filter parameters at query time
`executeView` takes only a view DeveloperName. There is no mechanism for the caller to inject runtime WHERE filters (e.g. "only risks for Account X"). The only filtering available is the static `Default_Filter__c` on the entity.

**Future state:** Add an optional `Map<String, Object> filters` parameter to `executeView`. The path resolver would inject caller-supplied filters into the root query and optionally into hop queries.

### 6. Junction hop ordering is fixed
For Junction traversals, the system always queries the junction object first and the target second. There is no support for traversing in the reverse direction (target → junction → source) without creating a separate Relationship record for the reverse direction.

### 7. Cross-object field paths are not supported
View Fields can only reference fields on the entity at that hop — not fields reachable via Salesforce's dot-notation (e.g. `Owner.Name`, `Account.BillingCountry` from a child). To include such fields, a new Entity and Relationship hop must be defined.

**Future state:** Add a `Traversal_Path__c` field to Semantic Field that supports simple dot-notation paths, resolved at query time via sub-selects or relationship queries.

---

## Deployment Footprint

| Artifact type | Count (current) |
|---------------|-----------------|
| CMDT object definitions | 7 |
| CMDT fields | ~50 across all types |
| Apex production classes | 9 |
| Apex test classes | 8 |
| LWC components | 2 |
| Remote Site Settings required | 0 (semantic layer itself makes no callouts) |

The semantic layer (registry, validator, security engine, path resolver, executor, flattener, orchestrator) makes zero HTTP callouts. Callouts are only made by inference consumers layered on top of it.

---

## Rebuild Checklist

If reconstructing from scratch:

1. Create the 7 CMDT object definitions with all fields as specified above. Pay attention to `Manageability` — `SubscriberControlled` fields must be set correctly or subscribers will not be able to override them.
2. Implement the Registry Service as a transaction-scoped singleton. Test that repeated calls within a transaction do not re-query.
3. Implement the Schema Validator with static caching. Test namespace-prefixed field resolution.
4. Implement the Security Engine. Test that FLS-inaccessible fields are stripped silently, not thrown.
5. Implement the Path Resolver. Test hop ordering and optional-hop resolution.
6. Implement the Query Executor for all three traversal types. Test junction hops specifically — they are the most complex.
7. Implement the Result Flattener with Cartesian product. Test optional vs. required hop behaviour (LEFT vs. INNER JOIN semantics).
8. Assemble the Orchestrating Service. Test the full pipeline end-to-end with a 3-hop view including one optional hop.
9. Add the LWC test harness (combobox → execute → datatable + metadata panel). This is the minimum viable UI for validating the system is working.
10. Seed CMDT records for at least one complete view (entities, relationships, semantic fields, view, hops, view fields) to validate the pipeline against real data.
