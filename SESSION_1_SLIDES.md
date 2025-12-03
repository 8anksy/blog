# Session 1: AI-Assisted Development Fundamentals
## Prompting as a Skill

---

## Slide 1: What is Prompting Really?

- Prompting is **programming with words**, not asking questions
- LLMs are **prediction engines**, not thinking machines
- Output quality = Instruction clarity
- This is a skill you can master

**Live Demo Coming:** See the difference between vague and specific prompts

---

## Slide 2: The Problem: Vague Prompts

- Generic prompt = Generic output
- The AI doesn't infer context
- It predicts the **statistically average** response
- This is not a limitation—it's how they work

**Example:** "Write me an apology email" → Generic template

**The Fix:** Make your pattern specific

---

## Slide 3: Live Demo 1 - Bad vs. Good Prompts

### Bad Prompt
```
Create an Apex trigger to handle account updates
```

**What you get:** Generic boilerplate with placeholders

---

## Slide 3b: Live Demo 1 - The Good Prompt

### Good Prompt
```
Create an Apex trigger that prevents duplicate Account names
in the same region.

Trigger context: before insert, before update

For before insert: Check if an Account with the same Name and
Region__c already exists. Prevent insert with error:
"An Account with this name already exists in this region"

For before update: Check if another Account (different Id) has
the same Name and Region__c. Prevent update with error message.

Use SOQL to query existing accounts.
Add error to Region__c field.
```

**What you get:** Specific, production-ready code

**Key Point:** Same AI, same capabilities, completely different output based on specificity

---

## Slide 4: Understanding the Mechanism

### How LLMs Actually Work

1. Convert prompt to tokens (text units)
2. Predict next token based on training patterns
3. Add prediction to context
4. Repeat until complete

It's **advanced autocomplete**, not reasoning.

### Pattern Specificity Rules
- Vague pattern → Vague prediction
- Specific pattern → Specific prediction
- Unclear pattern → Unreliable prediction
- Clear pattern → Accurate prediction

---

## Slide 5: Live Demo 2 - Iterative Refinement

### First Attempt (Vague)
```
Create a trigger that validates account data
```

Result: Generic validation for Name, BillingCity, Phone

Problem: Model predicted "common patterns", not **your** requirements

---

## Slide 5b: Live Demo 2 - Correction

### Refined Prompt
```
Update the trigger to validate only these fields:
- Account.Industry must not be null
- Account.AnnualRevenue must be > 0 if Industry is 'Technology'
- Account.Website must be populated if Rating is 'Hot'

Remove validation for Name, BillingCity, and Phone.
Use SOQL to query existing accounts.
Add error to Region__c field.
```

Result: Correct, specific validations

**Key Insight:** Off-track results mean your prompt needs refinement, not the AI

---

## Slide 6: Diagnosis Framework

When your output is wrong, ask:

1. **What context did I assume the model had?**
2. **What patterns in training data matched my vague input?**
3. **Where is my prompt ambiguous?**
4. **What specific information would eliminate this?**
5. **What assumptions am I making that aren't explicit?**

This is how you improve as a prompter.

---

## Slide 7: Beyond Simple Prompts

### Chain-of-Thought (CoT)
"Think through this step by step"
- Breaks complex patterns into sequential steps
- Each step is more constrained and accurate

### ReAct (Reason + Act)
- Think → Act → Observe → Repeat
- Tool use grounds predictions in real data
- This is what Cursor does automatically

---

## Slide 8: Live Demo 3 - Hidden Vagueness

### Spot the Vague Terms

```
Create a robust Apex batch job that validates and analyzes
Account data. The batch should intelligently process records
in an optimized manner. Validate all relevant fields for
data quality. Analyze account metrics to summarize key insights.
Handle errors gracefully with comprehensive logging.
Ensure the batch is scalable and performs well.
```

**What's wrong here?**

---

## Slide 8b: Hidden Vagueness Decoded

| Vague Term | Problem | Solution |
|---|---|---|
| "robust" | Undefined criteria | "Retries max 3 times, logs all failures" |
| "validates...data" | Which fields? Which rules? | "Validate Industry not null, AnnualRevenue > 0 if Tech, Website required if Rating='Hot'" |
| "intelligently process" | What intelligence? | "Process only Accounts modified in last 7 days" |
| "optimized manner" | Optimize what? | "Process 200 records per batch, use Map<Id, Account> for lookups" |
| "relevant fields" | Which fields exist? | List explicitly: "Name, Industry, AnnualRevenue, Website, Region__c, Rating" |
| "key insights" | What metrics? | "Total processed, failures by field, industries represented" |
| "comprehensive logging" | What gets logged? | "Batch start/end, records per chunk, final counts" |

**Takeaway:** Vague sounds sophisticated. Specific gets results.

---

## Slide 9: Prompting as Programming

This isn't a question:
```
Create test data for a multi-level Salesforce scenario
```

This is programming:
```
Generate Apex test data:

Parent Records:
- Create 3 Accounts: name = "TestAccount_[1-3]",
  Industry = 'Technology', AnnualRevenue = 1000000,
  BillingCity = 'San Francisco'

Child Records (Contacts):
- Create 2 Contacts per Account (6 total)
- Contact names: "TestContact_[A-F]"
- Email: "testcontact[a-f]@testdata.com"

Relationships:
- Each Contact linked to appropriate Account
- Create Opportunities via Account Contact Roles

Expected Outcome:
- 3 Accounts, 2 Contacts each
- 6 Opportunities total
- Insert order: Accounts → Contacts → Opportunities
```

**Explicit instruction = Specific output**

---

## Slide 10: Why This Matters

Programming approach provides:
1. **Hierarchical structure** - What records exist at each level
2. **Data specifications** - Field values, naming conventions
3. **Relationship mapping** - How things connect
4. **Insertion order** - Dependencies
5. **Verification** - Count expectations
6. **Error handling** - How to log failures

---

## Slide 11: The Mindset Shift

**Old Framework** → **New Framework**

"ChatGPT is inconsistently capable" → "Prompt clarity determines prediction accuracy"

"The AI understood my request" → "My pattern specification created a compatible prediction"

"I should ask the AI this question" → "I should program the AI with explicit instructions"

**This changes everything about how you approach prompting**

---

## Slide 12: Live Demo 4 - Comparative Test Plans

### Insufficient Prompt
```
Create a test plan for the Account sync feature
```

Problems:
- What's "sync"? (direction, scope, data types?)
- Which systems? (Salesforce to what?)
- Which fields? All? Certain ones? Relationships?
- What are edge cases?

Result: Generic test plan, missing your requirements

---

## Slide 12b: Live Demo 4 - Comprehensive Prompt

### Good Prompt
```
You are a QA lead creating a test plan for our
Salesforce-to-Warehouse sync feature.

Context: Every night at 2 AM UTC, our system syncs Account
records from Salesforce to our data warehouse. Read-only,
one-way sync.

Fields included: Name, Industry, AnnualRevenue, BillingCity,
plus custom fields: Region__c, PartnerTier__c, SyncStatus__c

Test Scope:
- Full sync on first run
- Incremental sync (past 24 hours changes)
- Field mapping correctness
- Null/empty handling
- Special characters (quotes, unicode, line breaks)
- Large numeric values (AnnualRevenue up to $999,999,999)
- New Accounts created post-sync
- Updated Accounts
- Deleted Accounts (soft vs. hard delete)
- Failure scenarios (timeout, API downtime, connection loss)

Format: Test Scenarios with steps, expected results, pass/fail criteria
Audience: QA engineers executing tests
Length: 2-3 pages, clear executable steps
```

Result: Test plan directly aligned with your feature

---

## Slide 13: Core Principles Summary

1. **Prompting is Pattern Programming**
   - Prompts are instructions, not questions
   - Output quality = Specification clarity

2. **LLMs Execute Statistical Pattern Matching**
   - Prediction engines, not reasoning systems
   - Pattern specificity determines accuracy

3. **Output Quality Reflects Instruction Clarity**
   - Poor output = Incomplete specifications
   - Not AI limitations—prompting skill gaps

---

## Slide 14: The Fundamental Rule

### Specific Patterns → Accurate Predictions
### Vague Patterns → Generic Predictions

Your job as a prompter:
- Establish maximum pattern specificity
- Eliminate assumptions
- Make implicit knowledge explicit
- Refine iteratively based on results

---

## Slide 15: Key Takeaways

✓ Prompting is a skill, not luck

✓ Vague prompts fail by design

✓ Specificity compounds output quality

✓ Iterative refinement is the workflow

✓ Off-track results = Prompt weakness, not AI weakness

✓ Treat it as programming, not asking

---

## Slide 16: Your Turn

Identify one prompt you use regularly that:
- Is vague or generic
- Produces inconsistent results
- Could benefit from more specificity

Rewrite it with:
- Explicit context parameters
- Specific field/format requirements
- Concrete examples
- Clear success criteria

See what changes.
