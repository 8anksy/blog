# Session 1: Fundamentals - What is Prompting Really?

**Duration:** 1 hour
**Prerequisites:** Basic familiarity with ChatGPT or similar LLMs
**Learning Objectives:**
- Understand prompting as a skill, not just asking questions
- Recognize LLMs as prediction engines, not thinking machines
- Grasp the mindset shift from "asking AI" to "programming it with words"

---

## Part 1: The Prompting Misconception (10 minutes)

### Why Most People Fail at Prompting

Common misconception: Prompting works like human conversation. A vague question produces a vague response. When a generic query like "Write me an apology email" produces generic output, the issue is not with the AI's capabilities—it is a prompting deficiency.

#### Root Cause: Lack of Specificity

Vague prompts fail because they lack essential information:
- No context about what occurred
- No information about the sender's identity or role
- No specified tone or style
- No defined audience
- Minimal instruction

The AI cannot infer missing context; it predicts the statistically average response.

#### The Skill Principle

Effective prompting requires treating poor outputs as a skill issue rather than an AI limitation. Prompt engineer Daniel Mesler articulates this principle: "If the AI model's response is bad, the problem is me." This perspective is the primary distinction between effective and ineffective prompters.

### Activity 1: Salesforce Apex Development Demo (5 minutes)

**Live Demo (Runnable in Cursor):**

#### Bad Prompt

```
Create an Apex trigger to handle account updates
```

**Result:** Generic boilerplate trigger without business logic clarity, error handling, or context-specific requirements.

```apex
trigger AccountUpdateTrigger on Account (before update, after update) {
    for (Account acc : Trigger.new) {
        // Handle account updates
    }
}
```

**Issues:**
- No trigger context specified (before/after, insert/update/delete)
- No business logic requirements defined
- No validation rules explained
- No integration context or dependencies mentioned
- Generic placeholder code

#### Good Prompt

```
Create an Apex trigger that prevents duplicate Account names in the same region.
Trigger context: before insert, before update
For before insert: Check if an Account with the same Name and Region__c already exists in the database. Prevent insert with error message: "An Account with this name already exists in this region"
For before update: Check if another Account (different Id) has the same Name and Region__c. Prevent update with error message: "An Account with this name already exists in this region"
Use SOQL to query existing accounts.
Add error to the field Region__c on the Account record being inserted/updated.
Include inline comments explaining the logic.
```

**Result:** Specific, testable trigger addressing actual business requirements.

```apex
trigger AccountDuplicatePreventionTrigger on Account (before insert, before update) {
    Set<String> namesToCheck = new Set<String>();
    Set<String> regionsToCheck = new Set<String>();

    for (Account acc : Trigger.new) {
        namesToCheck.add(acc.Name);
        if (acc.Region__c != null) {
            regionsToCheck.add(acc.Region__c);
        }
    }

    // Query existing accounts with same names and regions
    List<Account> existingAccounts = [
        SELECT Id, Name, Region__c
        FROM Account
        WHERE Name IN :namesToCheck
        AND Region__c IN :regionsToCheck
    ];

    // Create a map for quick lookup: Name + Region = existing account id
    Map<String, String> nameRegionMap = new Map<String, String>();
    for (Account acc : existingAccounts) {
        String key = acc.Name + '|' + acc.Region__c;
        nameRegionMap.put(key, acc.Id);
    }

    for (Account acc : Trigger.new) {
        String key = acc.Name + '|' + acc.Region__c;
        String existingId = nameRegionMap.get(key);

        if (Trigger.isInsert && existingId != null) {
            acc.addError('An Account with this name already exists in this region');
        } else if (Trigger.isUpdate && existingId != null && existingId != acc.Id) {
            acc.addError('An Account with this name already exists in this region');
        }
    }
}
```

**Specification Impact:**

The good prompt provides:
- Explicit trigger context (before insert, before update)
- Specific business rule (duplicate name + region check)
- Error message specification
- Technical implementation details (SOQL, field target)
- Expected behavior for both insert and update scenarios

The difference in output quality derives entirely from pattern specificity. Same AI, same capabilities, vastly different results based on instruction clarity.

**Key Concept:** Prompting is not a questioning process; it is pattern setting. Vague patterns require the AI to predict the statistically average Salesforce boilerplate. Specific patterns constrain prediction to align with actual business requirements.

---

## Part 2: LLMs are Prediction Engines (15 minutes)

### How LLMs Work: Core Mechanism

Large Language Models (LLMs) are probability engines, not reasoning systems. They predict the next token (a unit of text) based on patterns in training data. This is the fundamental principle underlying all prompting effectiveness.

#### Token Prediction Process

1. The prompt is converted to tokens (small text units)
2. The model evaluates all previous tokens to calculate the probability of the next token
3. A token is selected (typically the highest probability option, sometimes sampled from top probabilities)
4. The newly predicted token becomes part of the context for the next prediction
5. The process repeats until a stop token is generated

This token-by-token iterative process is essentially advanced predictive text—similar to phone autocomplete but vastly more sophisticated.

#### Prediction Quality and Pattern Specificity

LLM output quality directly correlates with pattern specificity in the prompt:
- Generic patterns → generic predictions
- Specific patterns → specific predictions
- Unclear patterns → unreliable predictions
- Clear patterns → accurate predictions

### Pattern Recognition and Prediction Accuracy

**Vague Salesforce Pattern (Model Goes Off Track):**
```
Create a trigger that validates account data
```

Result: The model predicts a generic pattern based on common trigger patterns in training data. It might produce validation for industry-standard fields (Name, BillingCity, Phone), but misses your actual business logic:

```apex
trigger AccountValidationTrigger on Account (before insert, before update) {
    for (Account acc : Trigger.new) {
        if (String.isBlank(acc.Name)) {
            acc.addError('Account name is required');
        }
        if (String.isBlank(acc.BillingCity)) {
            acc.addError('Billing city is required');
        }
        if (String.isBlank(acc.Phone)) {
            acc.addError('Phone is required');
        }
    }
}
```

**Issues:** Wrong validation rules. Model predicted generic best-practices, not your business requirements.

**Refined Pattern (Second Prompt - Correcting Course):**
```
Update the trigger to validate only these fields specific to our business:
- Account.Industry must not be null
- Account.AnnualRevenue must be > 0 if Industry is 'Technology'
- Account.Website must be populated if Rating is 'Hot'
Remove validation for Name, BillingCity, and Phone.
```

Result: Model now recognizes the specific pattern and generates correct validations:

```apex
trigger AccountValidationTrigger on Account (before insert, before update) {
    for (Account acc : Trigger.new) {
        if (acc.Industry == null) {
            acc.addError('Industry is required');
        }
        if (acc.Industry == 'Technology' && acc.AnnualRevenue <= 0) {
            acc.addError('Annual Revenue must be greater than 0 for Technology companies');
        }
        if (acc.Rating == 'Hot' && String.isBlank(acc.Website)) {
            acc.addError('Website is required for Hot prospects');
        }
    }
}
```

**Key Teaching Point:** The model does not "understand" your business logic. It recognizes patterns—common validation patterns, typical required fields, standard error messages. When the pattern is vague (validate account data), it predicts the statistically average validation. When you refine the pattern with specific business rules, it corrects course and predicts validations aligned with your actual requirements.

**Iterative Workflow:** This demonstrates the real workflow. You don't always need perfect one-shot prompts. Vague patterns produce off-track results, requiring refinement. Each refinement provides additional pattern constraint, correcting the prediction toward your intended output.

**Diagnostic Analysis - What Went Wrong:**

When the first prompt produced generic validation, the model went astray because:

1. **Missing context:** The prompt didn't specify your business domain constraints (Industry, AnnualRevenue, Rating fields are specific to your org)
2. **Ambiguous scope:** "Validates account data" matches any validation pattern in training data—the model predicted the most statistically common one (Name, City, Phone)
3. **No constraint specification:** Without explicit field names and rules, the model had infinite valid interpretations

**Improvement Framework - Diagnostic Questions:**

When a model produces off-track output, use these questions to improve your next prompt:

- **What context did I assume the model had?** (Example: Custom fields like Industry, AnnualRevenue, Rating. The model had no way to know these existed.)
- **What patterns in training data matched my vague input?** (Generic account validation uses standard fields. That's what the model predicted.)
- **Where is my prompt ambiguous?** (List: "validates account data" could mean any of 100 things.)
- **What specific information would eliminate this ambiguity?** (Field names, validation rules, conditional logic.)
- **What assumptions am I making that aren't explicit?** (Assuming the model knows your custom fields, your business rules, your data model.)

**This is how you improve as a prompter.** Each off-track result is a weakness in your prompt specification, not a weakness in the model. The model worked correctly—it predicted based on the vague pattern you provided. Your job is identifying where the pattern was vague and making it specific.

### Terminology: "Completions"

The term "completion" reflects the prediction mechanism. The output is a completion of the pattern initiated by the prompt, not an "answer" to a question. This distinction is critical:
- The AI predicts what comes next based on the pattern
- The prompter's responsibility is establishing clarity
- Pattern clarity directly determines prediction quality

### Structured Reasoning Through Pattern Enforcement

Apparent "reasoning" and "logic" in LLM outputs emerge from prompting techniques that structure output patterns:

#### Chain-of-Thought (CoT)

Explicitly instructing the LLM to decompose logic into sequential steps converts a single high-probability output into a sequence of intermediate steps. This approach increases accuracy by constraining predictions to follow logical progressions rather than predicting the final answer directly.

**Pattern Effect:** Breaking a complex pattern into sequential sub-patterns (steps) makes each individual prediction more constrained and accurate.

#### ReAct (Reason + Act)

Combining Chain-of-Thought with tool use creates an alternating pattern: reasoning step → action (tool call) → observation (tool result feedback) → next reasoning step. The model predicts actions, executes them externally, and incorporates results into subsequent predictions.

**Pattern Effect:** Grounding predictions in real external data (code context, file contents, execution results) replaces statistical prediction with evidence-based continuation.

#### Application in Cursor

Cursor (the IDE used by this team) implements ReAct patterns automatically:

1. **Reasoning Step:** The model generates analysis of the code context and user request
2. **Action Step:** The model issues commands to read files, execute tests, or analyze the codebase
3. **Observation Step:** Cursor returns file contents, test output, or analysis results
4. **Iteration:** The model incorporates observations into the next reasoning step

This loop transforms Cursor from a simple code completion tool into an iterative problem-solving system. The model's predictions become constrained by actual code state rather than training data patterns alone.

**Key Insight:** Cursor's power derives from embedding the LLM in a feedback loop where predictions must align with real code structure and execution results. Each tool call grounds the prediction pattern in evidence.

### Activity 2: Identifying Hidden Vagueness (7 minutes)

**Exercise: What's Wrong with This Prompt?**

Present this prompt to your team and ask: "What would you change about this prompt to make it more specific?"

```
Create a robust Apex batch job that validates and analyzes Account data.
The batch should intelligently process records in an optimized manner.
Validate all relevant fields for data quality and consistency.
Analyze account metrics to summarize key insights.
Handle errors gracefully with comprehensive logging.
Ensure the batch is scalable and performs well.
Include best practices throughout the implementation.
```

**Encourage participants to identify:**
- Which terms are vague?
- What context is missing?
- What assumptions is the prompt making?
- What output would this actually produce?

---

**Answer Key: Hidden Vagueness Breakdown**

| Vague Language | What It Actually Means | What's Missing | Better Specification |
|---|---|---|---|
| "robust Apex batch job" | Undefined robustness criteria | What makes it robust? Retry logic? Failure thresholds? | Specify: "Batch handles up to 10,000 records per chunk. Retries failed batches max 3 times. Logs all failures." |
| "validates...data" | Any validation could apply | Which fields? What rules? Custom field names? | List specific fields: "Validate Industry not null, AnnualRevenue > 0 if Industry='Tech', Website required if Rating='Hot'" |
| "intelligently process records" | Meaningless abstraction | What intelligence? Sorting? Filtering? Business logic? | Specify: "Process only Accounts modified in last 7 days. Skip records where Status='Inactive'" |
| "in an optimized manner" | No optimization criteria defined | Optimize for speed? Memory? Batch size? | Specify: "Process 200 records per batch. Use Map<Id, Account> for lookups to avoid repeated queries" |
| "relevant fields" | Assumes AI knows your schema | Which custom fields? Standard fields? | List explicitly: "Field names: Name, Industry, AnnualRevenue, Website, Region__c, Rating" |
| "key insights" | Too abstract | What metrics? What insights? Numbers? Percentages? | Specify: "Calculate: total accounts processed, validation failures by field, industries represented" |
| "handle errors gracefully" | Vague error handling | Log to Apex Logs? Custom object? What info? | Specify: "Log errors to Error_Log__c with: record ID, field name, validation rule violated, timestamp" |
| "comprehensive logging" | Undefined logging depth | What gets logged? When? How detailed? | Specify: "Log batch start/end, records processed per chunk, any skipped records, final counts" |
| "scalable and performs well" | No performance metrics | How many records? What's "well"? Execution time? Limits? | Specify: "Handle up to 100K records. Complete within 5 minutes. Track governor limit usage" |
| "best practices" | Training-data generic patterns | What practices? Framework-specific patterns? | Specify: "Use Database.executeBatch with size 200. Implement DML in finish() method. Query in chunks with LIMIT" |

**Why This Matters:**

A model given this "thorough-sounding" prompt will:
1. Predict the most statistically common Salesforce patterns (generic validation, standard error handling)
2. Miss your actual business logic entirely
3. Produce code that looks right but doesn't match requirements
4. Force you into iterative refinement anyway

**The Real Issue:** The prompt SOUNDS comprehensive. It uses sophisticated language like "intelligently," "robustly," "optimized." But every phrase is a placeholder for undefined decisions. The model must guess what each phrase means.

**Team Exercise Answer:**

A specific version might look like:

```
Create a Batch Apex class named AccountDataValidationBatch that processes Account records.

Scope: Process accounts modified in the last 7 days (use LastModifiedDate >= last_7_days).
Batch size: 200 records per batch.

Validations (log failures to Error_Log__c):
1. Industry field must not be null
2. AnnualRevenue must be > 0 if Industry = 'Technology'
3. Website must be populated if Rating = 'Hot'

Analysis/Metrics (log to Account_Metrics__c after batch completes):
- Total accounts processed
- Validation failures count
- Failures by validation rule
- Industries represented (breakdown by count)

Error Handling:
- Catch database exceptions in execute() method
- Log to Error_Log__c: record ID, field validated, rule violated, error message, timestamp
- Continue processing; don't stop on single record failure

Logging:
- Log batch start (timestamp)
- Log chunk completion (chunk X of Y, X records processed)
- Log batch finish (total processed, total failed, execution time)

Scheduling: Schedule this batch to run daily at 2 AM UTC.
Batch size: 200 records.
Governor limits: Track query count and DML operations in logs.
```

This version eliminates guessing. The model knows exactly what to build.

---

## Part 3: The Mindset Shift (10 minutes)

### Prompting as Programming

Prompting is not a question-asking process; it is programming with natural language. Dr. Jules White (Vanderbilt University) articulates this distinction: "A prompt isn't just a question. It's a program."

https://scholar.google.com/citations?user=10HSX90AAAAJ&hl=en

#### Programming vs. Asking

**Question approach:**
```
Create test data for a multi-level Salesforce scenario
```

**Programming approach:**
```
Generate Apex test data using the following specifications:

Parent Records:
- Create 3 Accounts: name = "TestAccount_[1-3]", Industry = 'Technology', AnnualRevenue = 1000000, BillingCity = 'San Francisco'

Child Records (Contacts):
- Create 2 Contacts per Account (6 total)
- Contact names: "TestContact_[A-F]"
- Email format: "testcontact[a-f]@testdata.com"
- Phone: "555-0001" through "555-0006"
- Each Contact linked to appropriate Account

Junction Records (Opportunities via Account Contact Roles):
- Create 2 Opportunities per Account (6 total): name = "TestOpp_[Account#]_[1-2]", StageName = "Prospecting", Amount = 50000, CloseDate = 90 days out
- For each Opportunity, create AccountContactRole records:
  - Role = "Business User" for Contact A of each Account
  - Role = "Decision Maker" for Contact B of each Account
  - Set IsPrimary = true for Contact A only

Expected Outcome:
- 3 Accounts with 2 Contacts each
- 6 Opportunities (2 per Account)
- 12 AccountContactRole junction records linking Contacts to Opportunities via Account

Insert all records in order: Accounts → Contacts → Opportunities → AccountContactRoles
Use try-catch with System.debug for any DML errors
```

The programming approach establishes explicit instructions:
1. **Hierarchical structure** - What records exist at each level (Accounts → Contacts → Opportunities → Roles)
2. **Data specifications** - Field values, naming conventions, calculated dates
3. **Relationship mapping** - Explicit "per Account" and "per Opportunity" multipliers
4. **Junction logic** - Which Contact maps to which Role on which Opportunity
5. **Insertion order** - Sequence required to respect foreign keys (parents before children)
6. **Verification** - Count expectations (3 Accounts, 6 Contacts, 6 Opps, 12 junction records)
7. **Error handling** - How to log and handle failures during data creation

#### Implications

This perspective shift removes perceived randomness:
- **Control:** The prompter defines output parameters through explicit instruction
- **Precision:** Minor details significantly affect outcomes
- **Iteration:** Refinement is often necessary
- **Skill-based:** Output quality depends on instruction clarity, not chance

### LLM vs. Human Processing

LLMs operate through statistical pattern matching, not reasoning.

**Human Process:**
1. Analyze situation context
2. Reason about cause and effect
3. Apply empathy and judgment
4. Formulate response based on understanding
5. Communicate response

**LLM Process:**
1. Receive prompt (pattern specification)
2. Predict next token based on training data patterns
3. Iterate prediction until completion
4. Return predicted sequence

This distinction explains observed LLM behaviors:
- **Vague prompts fail:** Multiple patterns match; prediction lacks constraint
- **Specific prompts succeed:** Fewer patterns match; prediction is constrained
- **Examples improve output:** Examples establish explicit pattern templates
- **Hallucinations occur:** The model predicts plausible continuations regardless of factual accuracy

### Activity 3: Conceptual Framework Shift (5 minutes)

**Perspective Comparison:**

Old Framework → New Framework

1. "ChatGPT is inconsistently capable" → "Prompt clarity determines prediction accuracy"

2. "The AI understood my request" → "My pattern specification created a compatible prediction"

3. "I should ask the AI this question" → "I should program the AI with explicit instructions"

**Reflection prompts:**
- How does treating prompting as programming change your approach?
- Identify one area where your current prompts are overly vague
- What explicit parameters could you add to a typical prompt you use?

---

## Part 4: Bringing It Together with an Example (10 minutes)

### The Salesforce Test Plan: Comparative Analysis

#### Insufficient Prompt

```
Create a test plan for the Account sync feature
```

**Issues:**
- No definition of what "sync" entails (direction, scope, data types)
- No specification of which systems sync (Salesforce to what?)
- No test scope (all fields? certain fields? relationships?)
- No definition of edge cases or failure modes
- No specification of testing format or audience

**Result:** Generic test plan covering common scenarios but missing your actual requirements

#### Comprehensive Prompt

```
You are a QA lead creating a test plan for our Salesforce-to-Warehouse sync feature.
Context: Every night at 2 AM UTC, our system syncs Account records from Salesforce to our data warehouse. This is read-only—data flows one direction only. The sync includes Account standard fields (Name, Industry, AnnualRevenue, BillingCity) plus three custom fields: Region__c, PartnerTier__c, and SyncStatus__c.

Test Scope:
- Full sync on first run (all existing Accounts)
- Incremental sync on subsequent runs (only changed/new Accounts in past 24 hours)
- Field mapping correctness (all fields arrive in warehouse with correct values)
- Null/empty field handling
- Special characters in text fields (quotes, unicode, line breaks)
- Large numeric values (AnnualRevenue up to $999,999,999)
- New Accounts created in Salesforce post-sync start
- Updated Accounts (field changes detected and reflected)
- Deleted Accounts (soft delete vs. hard delete behavior)
- Sync failure scenarios (network timeout, Salesforce API downtime, warehouse connection loss)

Format: Organized as Test Scenarios with steps, expected results, and pass/fail criteria.
Audience: QA engineers who will execute these tests.
Keep it to 2-3 pages with clear, executable steps.
```

**Improvements:**
- Explicit system scope (Salesforce → Warehouse, one-way)
- Specific field list (standard + custom fields named)
- Defined test categories (full sync, incremental, validation, edge cases, error handling)
- Concrete examples (numeric bounds, special character types)
- Clear format and audience expectations

**Result:** Test plan directly aligned with your actual feature requirements, executable without interpretation

#### Specification Impact

Each additional specification reduces prediction ambiguity and improves output accuracy. The difference between generic and specific output derives solely from pattern clarity.

---

## Part 5: Summary and Key Takeaways (5 minutes)

### Core Principles

1. **Prompting is Pattern Programming:** Prompts function as instructions, not questions. Output quality depends on specification clarity.

2. **LLMs Execute Statistical Pattern Matching:** LLMs are not reasoning systems; they predict token sequences based on training data patterns. Pattern specificity determines prediction accuracy.

3. **Output Quality Reflects Instruction Clarity:** Poor output results from incomplete specifications, not AI limitations.

### Fundamental Rule

Specific patterns produce accurate predictions. Vague patterns produce generic predictions. Prompter responsibility is establishing maximum pattern specificity and clarity.

---

## Homework/Reflection Questions

1. **Prompt Revision Analysis:** Identify a previous prompt that produced inadequate results. Rewrite incorporating:
   - Role/persona specification
   - Explicit context parameters
   - Format and tone requirements

   Document changes in output quality.

2. **Conceptual Framework Comparison:** Contrast the following approaches:
   - Question-based: "I'm asking an AI to do something"
   - Programming-based: "I'm programming an AI to predict specific output"

   Analyze how this framework shift affects prompt design.

3. **Vague Prompt Analysis:** Evaluate the prompt "Write a marketing email"
   - Identify all undefined dimensions requiring AI inference
   - Specify how each dimension could be made explicit

4. **Specification Clarity Exercise:** Select a work task and document:
   - Desired output specification
   - Required sender perspective/role
   - Necessary contextual information
   - Output format and constraints

   Compare ease of thought clarification vs. prompt articulation.