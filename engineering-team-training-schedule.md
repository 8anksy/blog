# Engineering Team AI Training Schedule
## Individual 1-Hour Sessions

---

## **Foundation Sessions (Prerequisite Knowledge)**

### Session 1: Vibe Coding vs. AI-Assisted Engineering
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Understand the distinction between vibe coding and professional AI-assisted development
- Learn why specs matter for derisk-ing AI usage
- Recognize the 70% problem and why it matters

**Key Concepts**:
- Vibe coding: rapid exploration, acceptable for prototypes only
- AI-assisted engineering: human remains responsible, AI is force multiplier
- The last 30%: maintainability, security, edge cases
- Experienced engineers finish the 30% easier than juniors

**Activities**:
- Watch/discuss key insights from Addy Osmani's perspective
- Identify 2-3 examples from your own codebase of "70% solutions"

---

### Session 2: Critical Thinking & Professional Responsibility
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Understand why engineers must comprehend every line they ship
- Learn the difference between "it works" and "I understand it"
- Recognize the professional standard for AI-assisted code

**Key Concepts**:
- You must be able to explain and defend every line you ship
- Reading the thinking/reasoning from AI models is essential
- Debugging manually is a core differentiator for professionals
- Professional leverage comes from expertise, not prompting ability

**Activities**:
- Review an AI-generated code snippet together
- Practice explaining it to the group
- Identify what would happen if you couldn't debug it

---

### Session 3: Specification Excellence
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn why clear specs reduce iteration and improve outcomes
- Understand the relationship between specification clarity and AI output quality
- Shift mindset from "how fast can I write?" to "how clearly can I articulate what I need?"

**Key Concepts**:
- Ambiguity in specs gets amplified by AI, not resolved
- Adding detail enhances clarity if it encodes business logic
- Templates without reasoning frameworks = template-filling, not thinking
- Every document should enable a specific decision

**Activities**:
- Audit one current spec from your team
- Identify vague language and rewrite it with specificity
- Define success criteria for the spec

---

### Session 4: Information Architecture as Strategy
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to structure documents so they encode reasoning, not just information
- Understand why information integrity matters to AI systems
- Design documents that force clarity

**Key Concepts**:
- Document structure should reflect decision flows, not just sections
- Templates hide vague thinking; structure forces clarity
- Make implicit assumptions explicit
- Information architecture is business logic

**Activities**:
- Redesign one team document (RFC, runbook, or architecture decision)
- Map the decision framework it needs to support
- Document hidden assumptions in your domain

---

### Session 5: Defining Quality Through Failure Cases
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to identify and catalog failure modes in your domain
- Use concrete examples to guide AI (and human) behavior
- Build team-specific evaluation criteria

**Key Concepts**:
- "Make it better" is useless feedback; failure examples are actionable
- 5-7 concrete examples reveal what your team actually values
- Failure modes teach more than success examples
- Catalogs become your team's quality standard

**Activities**:
- Collect 5-7 real failure examples from your team's work
- Label what made each one bad
- Build a "documentation problems catalog" for your domain

---

## **Prompting & Mental Models Sessions**

### Session 6: Self-Correction Systems - Chain of Verification
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Understand how to force models to critique their own outputs
- Learn to embed verification loops in prompts
- Move beyond single-pass generation limitations

**Key Concepts**:
- Single-pass generation is a fundamental limitation of LLMs
- Structure the generation process to include mandatory self-critique
- Chain of verification activates patterns the model was trained on
- This is not "asking the model to be more careful" (too vague)

**Techniques**:
1. Have model generate output
2. Ask it to identify ways analysis might be incomplete
3. Cite specific evidence for each concern
4. Revise based on verification

**Activities**:
- Practice building a chain of verification prompt for a code review
- Compare output quality: single-pass vs. with verification
- Apply to 1-2 real team tasks

---

### Session 7: Adversarial Prompting
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn aggressive self-correction techniques
- Use adversarial prompting for security-critical work
- Expose problems even when the model would skip them

**Key Concepts**:
- More aggressive than chain of verification
- Demands models find problems even if stretching
- Use when completeness is absolutely essential
- Particularly valuable for architecture and security reviews

**Techniques**:
1. Ask model to "attack" its previous work
2. Demand specific number of vulnerabilities/issues
3. Require likelihood and impact assessment for each
4. Synthesize recommendations that address all concerns

**Activities**:
- Practice building adversarial prompts for your domain
- Identify 3-5 scenarios where adversarial prompting would be valuable
- Run one real security or architecture review with this technique

---

### Session 8: Few-Shot Prompting & Edge-Case Learning
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to teach models boundary conditions through examples
- Understand how subtle failure modes improve categorization
- Build example sets that reduce false negatives

**Key Concepts**:
- Boundary conditions are hard to describe in words alone
- Models learn better from examples of failure modes than rules
- Progress from obvious failures → subtle failures teaches discrimination
- What "looks correct" vs. what "is correct" requires examples

**Techniques**:
1. Show obvious failure mode (baseline)
2. Show subtle failure mode (teaches the edge)
3. Include correct examples too
4. Let examples teach the discrimination

**Activities**:
- Build a few-shot example set for a security issue your team cares about
- Test output quality: with vs. without examples
- Document patterns that work in your domain

---

### Session 9: Reverse Prompting
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to ask the model to design optimal prompts
- Exploit the model's meta knowledge about prompting
- Improve prompt quality through meta-prompting

**Key Concepts**:
- Models are trained on prompting conversations
- You can ask models to design prompts for specific tasks
- The model can design AND execute in one interaction
- Include your requirements in the meta-prompt

**Techniques**:
1. Position model as expert prompt designer
2. Define the specific task needing a prompt
3. Ask what details/reasoning/output matters
4. Request execution on your actual task

**Example**:
"You're an expert prompt designer. Design the single most effective prompt to [task]. Consider what details matter, what output format is most actionable, what reasoning steps are essential. Then execute that prompt on this [content]."

**Activities**:
- Practice building meta-prompts for 2-3 team tasks
- Compare reverse-prompt output to hand-written prompts
- Document what the model suggests that improves your prompts

---

### Session 10: Recursive Prompt Optimization
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to systematically improve prompts across specific dimensions
- Understand how to guide prompt evolution without dictating output
- Build iterative improvement into your prompting workflow

**Key Concepts**:
- Define specific dimensions you care about (constraints, ambiguities, reasoning depth)
- Let the model improve across those dimensions
- Multiple passes in single interaction
- Don't dictate the new prompt—guide the improvement axes

**Techniques**:
1. State the goal and current prompt
2. Define version-specific improvement goals
3. Let model iterate through versions
4. Collect improvements from all versions

**Example Versions**:
- Version 1: Add missing constraints
- Version 2: Resolve ambiguities
- Version 3: Enhance reasoning depth

**Activities**:
- Take one of your team's prompts
- Run recursive optimization on it
- Compare original vs. final versions
- Measure output quality improvement

---

### Session 11: Deliberate Over-Instruction
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to counter models' training toward brevity
- Expose model reasoning for examination
- Get exhaustive depth when you need it

**Key Concepts**:
- LLM training optimizes for conciseness
- This can prematurely collapse reasoning chains
- Over-instruction combats this without being crude
- Use when you want to think WITH the model, not just get an answer

**Techniques**:
1. Explicitly request exhaustive depth
2. List what you don't want (summaries, conciseness, hedging)
3. List what you do want (implementation details, edge cases, context)
4. Emphasize completeness over brevity

**Example**:
"Do not summarize. Expand every single point with implementation details, edge cases, failure modes, and historical context. I need exhaustive depth. Prioritize completeness."

**Activities**:
- Compare standard prompt vs. over-instruction version
- Identify what new insights emerge with exhaustive depth
- Use for 1-2 architectural decisions

---

### Session 12: Zero-Shot Chain of Thought Scaffolding
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to structure model thinking through templates
- Force problem decomposition through scaffolding
- Verify the model is on the right track

**Key Concepts**:
- LLMs continue patterns naturally
- Blank templates trigger automatic structure-filling
- Model decomposes problem following your scaffold
- Particularly effective for technical problems

**Techniques**:
1. List questions model needs to answer
2. Order them logically for problem-solving
3. Leave blanks for model to fill
4. Model structures thinking around your scaffold

**Example for root-causing**:
1. What's the observed symptom?
2. What subsystems could produce this?
3. For each, what's the mechanism?
4. Which is most likely and why?
5. How do we verify?

**Activities**:
- Build scaffolds for 2-3 types of problems your team faces
- Run model through your scaffold
- Verify model reasoning is sound before acting on output

---

### Session 13: Reference Class Priming
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to set quality benchmarks through examples
- Prime models toward consistent output quality
- Use high-quality examples instead of rules

**Key Concepts**:
- Models are associative—examples prime their output
- Show reasoning quality, not input-output pairs
- Priming reduces quality variance across outputs
- Different from traditional few-shot (which teaches what to do)

**Techniques**:
1. Select high-quality reasoning example (yours or model's best work)
2. Show it to the model
3. Ask for output matching that quality bar
4. Model distributes toward that quality level

**Activities**:
- Identify your team's highest-quality analysis
- Use it as a reference class primer
- Measure output quality consistency improvement
- Build 2-3 reference class examples for recurring tasks

---

### Session 14: Multi-Persona Debate
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to simulate competing viewpoints
- Expose blind spots through debate
- Surface considerations you wouldn't generate alone

**Key Concepts**:
- Single perspective has blind spots by definition
- Competing viewpoints force comprehensive analysis
- Personas need specific, conflicting priorities
- Synthesize after debate, don't just pick a winner

**Techniques**:
1. Define 3+ personas with explicit priorities
2. State conflicting goals/constraints
3. Require argument and critique between personas
4. Synthesize final recommendation addressing all concerns

**Example for vendor decision**:
- Persona 1: Cost optimizer (priority: minimize spend)
- Persona 2: Reliability engineer (priority: uptime and support)
- Persona 3: Ops lead (priority: integration effort)

**Activities**:
- Run multi-persona debate on 1-2 real team decisions
- Document perspectives that wouldn't have emerged otherwise
- Compare to single-perspective analysis

---

### Session 15: Temperature Simulation
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to simulate API temperature control in chat
- Get both exploratory and focused analysis
- Synthesize insights from different reasoning modes

**Key Concepts**:
- Temperature (API parameter) controls determinism vs. creativity
- Low temperature = focused, direct, deterministic
- High temperature = creative, exploratory, uncertain
- You can simulate both in a single conversation

**Techniques**:
1. Request high-temperature pass (exploratory, uncertain)
2. Request low-temperature pass (confident, concise)
3. Ask model to synthesize and highlight where each is warranted
4. Combine benefits of both approaches

**Example**:
"First, I want a junior analyst who is uncertain and overexplains. Then a confident expert who is concise and direct. Finally, synthesize both and highlight where uncertainty is warranted and where confidence is justified."

**Activities**:
- Practice temperature simulation on a complex architectural decision
- Compare three-pass output to single-pass
- Use synthesis to guide your actual decision-making

---

## **Tool & Workflow Sessions**

### Session 16: Cursor Commands - Deep Dive
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Master Cursor's command system for productivity
- Learn to compose complex commands for recurring workflows
- Build team-specific command library

**Prerequisites**: Session 1-3
**Content**: Advanced patterns beyond basic usage

**Key Concepts**:
- Commands automate repetitive prompting patterns
- Composition chains multiple commands
- Context from your codebase amplifies command effectiveness
- Team-shared commands reduce onboarding friction

**Techniques**:
1. Identify recurring prompt patterns in your workflow
2. Abstract them into reusable commands
3. Compose commands into larger workflows
4. Document and share with team

**Hands-On Workshop**:
- Audit your personal workflow: what do you prompt repeatedly?
- Build 3 custom Cursor commands for those patterns
- Share with team and refine based on feedback

**Deliverable**:
- Team command library (documented, shareable)

---

### Session 17: Multi-Agent Workflows
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Understand agent orchestration and parallel execution
- Learn patterns for multi-agent coordination
- Handle merge conflicts and context boundaries

**Prerequisites**: Session 1-2
**Content**: Patterns from experience with Jude, CodeEx, GitHub agents

**Key Concepts**:
- Agents are historically unprecedented (developers couldn't parallelize like this before)
- Parallel agents ≠ context-switching (different cognitive demand)
- Merge conflict-free operations are critical
- Human attention remains the bottleneck

**Patterns**:
1. **Sequential agents**: One completes, passes to next (good for pipelines)
2. **Parallel agents**: Multiple working concurrently (requires conflict-free work)
3. **Orchestration**: Human monitors/coordinates multiple agents (like managing team of juniors)

**Effective Use Cases**:
- Writing/updating tests
- Library version migrations
- Smaller, isolated changes
- Well-scoped features with clear dependencies

**Activities**:
- Map one of your team's processes to agent workflow
- Identify parallelizable work
- Design merge conflict resolution strategy
- Simulate multi-agent run through that workflow

**Deliverable**:
- One working multi-agent workflow for your team

---

### Session 18: Google MCP - Deep Dive
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Understand MCP (Model Context Protocol) and its capabilities
- Learn what Google MCPs enable
- Integrate MCPs into your AI workflows

**Prerequisites**: Session 1-2
**Content**: MCP architecture and available tools

**Key Concepts**:
- MCP standardizes how tools integrate with AI models
- Google MCPs give models access to Google's ecosystem
- Context provided by MCPs is richer than prompting alone
- Tools reduce hallucination by grounding model in reality

**Available Google MCPs** (examples):
- Google Search MCP
- Google Drive MCP
- Google Workspace MCPs
- Custom domain-specific MCPs

**How MCPs Help**:
- Real-time information access (not training data dependent)
- Grounding analysis in actual data
- Reducing hallucination through tool use
- Enabling actions (not just analysis)

**Architecture**:
1. Model generates tool calls (not text)
2. MCP executes tools in actual systems
3. Results fed back to model
4. Model continues reasoning with real data

**Activities**:
- Identify 3 recurring tasks where MCP access would help
- Research available MCPs for your tech stack
- Prototype one MCP integration
- Document results and lessons learned

**Deliverable**:
- Working MCP integration for your workflow

---

### Session 19: Building Custom MCPs
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to build custom MCPs for team-specific tools
- Understand when custom MCPs add value
- Deploy and iterate on MCPs

**Prerequisites**: Session 18
**Content**: MCP development and best practices

**Key Concepts**:
- Custom MCPs solve domain-specific problems
- Build when standard MCPs don't meet your needs
- Tool use is more efficient than prompting alone
- Grounding in your actual systems reduces errors

**When to Build Custom MCPs**:
- Recurring tool calls to proprietary systems
- Integration with internal services
- Domain-specific operations teams need
- Significant velocity improvements possible

**MCP Development Process**:
1. Define the capability the tool provides
2. Specify input/output contract
3. Implement tool logic
4. Test with model interaction
5. Iterate based on actual usage

**Example**:
A team might build an MCP that:
- Queries your issue tracker
- Fetches related code
- Retrieves architectural context
- Enables model to understand work in full organizational context

**Activities**:
- Identify one candidate for custom MCP in your workflows
- Design the MCP interface
- Prototype implementation
- Test with team and refine

---

## **Code Quality & Integration Sessions**

### Session 20: The Human Bottleneck in Code Review
**Duration**: 1 hour
**Audience**: All engineers, especially leads
**Objectives**:
- Understand why human review becomes bottleneck with AI
- Learn strategies to manage review velocity
- Maintain quality while scaling throughput

**Key Concepts**:
- AI increases code velocity; human review becomes bottleneck
- AI writing code + AI reviewing code = dangerous feedback loop
- External contributors using LLMs strain maintainers
- Senior engineers' review capacity becomes leverage

**The Problem**:
- More code than ever, but not proportionally more understanding
- Copy-pasting AI code without comprehension = risk
- If AI writes AND reviews, how sure are you about quality?
- Professional responsibility: you own what you ship

**Strategies**:
1. **Structured reviews**: Use evaluation criteria (from failure cases)
2. **Spot checks**: Random deep dives, not everything thorough
3. **Tiered reviews**: Different depth for different risk levels
4. **Automation**: Use AI to flag issues, human to decide
5. **Teaching reviews**: Seniors mentor juniors on code understanding

**Activities**:
- Audit current PR review bottleneck
- Identify highest-risk code patterns from AI
- Build evaluation criteria for code review
- Implement spot-check sampling strategy

---

### Session 21: Testing Strategies for AI-Generated Code
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn testing patterns that derisk AI-generated code
- Use tests as guardrails and communication tools
- Keep projects "green" throughout AI-assisted development

**Key Concepts**:
- Tests prove things work (not assumptions)
- Tests clarify what broke when things fail
- Tests prevent undetected issues reaching production
- Tests = communication tool for "what should happen"

**Testing Patterns**:
1. **Spec-driven**: Write tests first, then generate code to pass them
2. **Incremental**: Small, verifiable chunks with tests
3. **Edge-case heavy**: Test the 30% (edge cases, errors, boundaries)
4. **Integration-focused**: How does generated code fit in your system?

**Effective Approach**:
1. Write clear tests (define expected behavior)
2. Use tests as spec for code generation
3. Generate code to pass tests
4. Run tests throughout development
5. Red → Green → Refactor cycle maintains clarity

**Activities**:
- Identify 1-2 features your team builds frequently
- Write comprehensive test suites for them
- Generate code to pass tests
- Compare quality vs. code-first generation

---

### Session 22: Spec-Driven Development Framework
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Master spec-driven development as foundation for AI-assisted work
- Learn to write specs that AI can execute effectively
- Measure improvement in code quality and velocity

**Key Concepts**:
- Clear specs + AI execution = higher quality than vague specs + human execution
- Specs force thinking before coding
- AI amplifies clarity (good specs → good code; bad specs → bad code)
- Specs become source of truth for requirements

**The Pattern**:
1. **Plan**: Define what you're building (clear, written)
2. **Specify**: Write actual expectations and requirements
3. **Delegate**: Hand to AI with clear specification, not vague vibes
4. **Verify**: Test against spec
5. **Refactor**: Improve quality within spec bounds

**Building Good Specs**:
- What's the problem you're solving?
- What are success criteria?
- What constraints exist?
- What edge cases matter?
- What examples illustrate correct behavior?

**Activities**:
- Write spec for next feature your team builds
- Have AI implement against spec
- Measure against traditional vague assignment
- Document what improved

---

### Session 23: Code Review Best Practices with AI
**Duration**: 1 hour
**Audience**: All engineers, especially leads
**Objectives**:
- Establish code review patterns that work with AI-generated code
- Avoid the trap of AI-reviewing-AI
- Maintain psychological safety during transition

**Key Concepts**:
- Human review remains central to quality
- AI can assist review (flagging issues), not replace it
- Thorough reading is professional responsibility
- Review is where standards become enforceable

**Best Practices**:
1. **Require deep reading**: "Did you actually understand this?"
2. **Spot-check logic**: Ensure AI reasoning is sound
3. **Test verification**: Is behavior guaranteed by tests?
4. **Edge case verification**: Are boundaries covered?
5. **Style consistency**: Does it match team patterns?

**Review Prompts**:
- Can you explain what this code does in plain English?
- What would break if we removed this line?
- How does this fit into the broader system?
- What edge cases could cause problems?
- Is there a simpler way to express this intent?

**Activities**:
- Review recent AI-generated PR
- Document questions/concerns
- Refine code with author
- Document the review process and improvements made

---

## **Integration & Application Sessions**

### Session 24: Voice, Conviction & Communication
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Move beyond AI's default bland voice
- Establish team standards for clear, convincing communication
- Use AI as writing assistant, not replacement

**Key Concepts**:
- AI default: diplomatically hedged, pseudo-comprehensive, bland
- Engineering communication should carry conviction and specificity
- "Here's why" > "here are the options"
- Uncertainty should be explicit, not hidden in hedging

**What Good Engineering Communication Includes**:
- **Clear bets**: What you're confident about
- **Explicit uncertainty**: Where ambiguity exists and why
- **Specificity**: Concrete details, not generalizations
- **Intent**: Why this matters to the reader

**Patterns to Avoid**:
- Hedging when you shouldn't ("may improve" vs. "will improve")
- Pseudo-comprehensive coverage (trying to explain everything equally)
- Diplomatic fence-sitting (saying both sides equally valid when they're not)
- Missing failure modes (glossing over risks)

**Using AI for Communication**:
- Write your intent and conviction first
- Use AI to refine language, add structure, catch gaps
- Never use AI to "generate the full document"
- Always override AI default voice with your own conviction

**Activities**:
- Write 1-2 architectural decisions in your voice
- Identify where AI would hedged/genericize
- Establish team communication standards
- Review and refine together

---

### Session 25: Intent-Driven Prompting Framework
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Master the complete framework for high-quality prompts
- Apply framework consistently across your work
- Build team standard for prompting

**Prerequisites**: Sessions 6-15
**Content**: Integration of all prompting techniques

**The Framework** (6 Elements):

1. **Purpose**: Why are we doing this? What's the goal?
2. **Context**: Where/how will this be used?
3. **Logical Structure**: What decision/reasoning framework guides this?
4. **Constraints**: What are boundaries? (length, tone, scope, technical)
5. **Evaluation Criteria**: What does success look like?
6. **Failure Tests**: What examples of bad output do we want to avoid?

**Applying the Framework**:

Start with all 6 elements → Run prompts through them → Evaluate quality → Iterate on elements

**Example: Architecture Decision Record**

- **Purpose**: Enable team to make database choice decision
- **Context**: Review in architecture meeting, shared with backend team
- **Structure**: [Problem] → [Options] → [Trade-offs] → [Decision] → [Consequences]
- **Constraints**: Max 2 pages, don't recommend without analysis, explicit about unknowns
- **Evaluation**: Decision maker is clear, trade-offs are concrete, consequences are actionable
- **Failure Tests**: Don't recommend all options equally, avoid vague language, include failure modes

**Activities**:
- Take one recurring type of output your team generates
- Apply full framework to it
- Build template with all 6 elements
- Share with team, refine iteratively

---

### Session 26: Building Team Failure Case Catalog
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Systematize quality standards through concrete examples
- Build team-specific evaluation criteria
- Use failure cases to guide both AI and human work

**Prerequisites**: Session 5
**Content**: Team-specific failure cases

**The Approach**:
1. Collect real failures from team's actual work (5-7 examples minimum)
2. Label what made each one bad specifically
3. Build evaluation criteria from patterns
4. Use catalog as quality guide

**Failure Case Categories**:
- Technical spec failures (over-specified, missing constraints)
- Architecture decision failures (vague trade-offs, missing consequences)
- Documentation failures (not actionable, too theoretical)
- Code failures (edge cases, security issues, maintainability)
- Communication failures (unclear intent, missing context)

**For Each Failure Case, Document**:
- What was the output?
- What specifically made it bad?
- What pattern does this reveal?
- What would "good" look like?
- How would we prevent this?

**Using the Catalog**:
- Prompt engineering: "Here's what bad looks like; don't generate this"
- Code review: "This is a failure case pattern we've seen"
- Team discussions: "Remember when we had this problem?"
- Evaluation: "Does this output match any failure patterns?"

**Activities**:
- Collect 5-7 real failures from your team
- Build shared document with labeled patterns
- Categorize by type/domain
- Use in 2-3 actual prompts or reviews

---

### Session 27: Iteration Diagnosis & Intent Communication
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to diagnose why iterations fail
- Understand the relationship between intent clarity and output quality
- Shift from "more iterations" to "better intent definition"

**Key Concepts**:
- "Make it better" feedback is useless
- When AI output misses, usually specification clarity was lacking, not model capability
- Better iteration comes from better upfront intent definition
- Investing in clarity pays off exponentially

**Iteration Antipattern**:
1. Vague initial request
2. AI produces mediocre output
3. "Make it better" feedback
4. AI produces different mediocre output
5. Repeat cycle (frustrating for everyone)

**Better Pattern**:
1. Define intent clearly (6-element framework)
2. Run prompt once
3. If output misses mark, diagnose the spec
4. Refine spec, not retry prompt
5. Build better specs over time

**Diagnosis Questions**:
When AI output misses:
- Did I communicate my intent clearly?
- What assumptions did I make that weren't stated?
- What examples would help?
- What constraints did I miss?
- What failure modes should I rule out?

**Continuous Improvement**:
- Track iteration patterns
- What specs get it right first try?
- What specs require many iterations?
- Refine your specs based on patterns

**Activities**:
- Review 3-5 recent AI iterations that required multiple tries
- Diagnose specification clarity issues
- Rewrite specs with more clarity
- Re-run and compare iteration count
- Document what improved

---

### Session 28: Context Engineering
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Learn to optimize your context window for better outcomes
- Understand what context matters most
- Build context-rich prompts

**Key Concepts**:
- Short prompts miss organizational context
- LLMs trained on permissively licensed code (lowest common denominator)
- Real product development is accumulated context
- Context engineering is as important as prompt engineering

**What Context Matters**:
- **Codebase context**: Patterns, conventions, existing implementations
- **Domain context**: Why problems matter, how decisions interrelate
- **History context**: Bug patterns, past decisions, lessons learned
- **System context**: Architecture, dependencies, performance characteristics
- **Team context**: Standards, values, priorities

**Context Sources**:
- Code examples from your codebase
- Architecture documents
- Runbooks and patterns you've established
- Issue tracking (why problems matter)
- Team discussions and ADRs
- Test cases (what should/shouldn't happen)

**Optimizing Context Window**:
1. Identify what context the model needs
2. Prioritize by relevance (most relevant first)
3. Trim redundant context
4. Use structure to make context scannable
5. Include examples from your actual codebase

**MCPs Provide Context**:
- Google MCPs ground models in real data
- Custom MCPs provide domain-specific context
- Tools reduce hallucination by making context available as tools

**Activities**:
- Audit your typical prompts
- Identify missing context
- Gather relevant context (code examples, docs, patterns)
- Rebuild prompt with richer context
- Measure quality improvement

---

### Session 29: Debugging & Problem-Solving When AI Fails
**Duration**: 1 hour
**Audience**: All engineers, especially juniors
**Objectives**:
- Build confidence debugging AI-generated code
- Learn systematic approaches to understanding what went wrong
- Develop independent problem-solving skills

**Key Concepts**:
- AI sometimes generates code that looks right but doesn't work
- Manual debugging is a core professional skill
- Understanding root causes is more valuable than asking for regeneration
- Professional leverage comes from being able to fix what AI produces

**Debugging Checklist**:
1. **Reproduce**: Can you reliably trigger the problem?
2. **Isolate**: What's the smallest change that exhibits the problem?
3. **Understand**: Read the code—what should it do?
4. **Trace**: Step through execution—where does it diverge?
5. **Hypothesize**: What assumption in the code is wrong?
6. **Test**: Verify your hypothesis
7. **Fix**: Change the assumption
8. **Verify**: Confirm fix works

**Tools & Techniques**:
- Print statements / logging
- Browser DevTools (for frontend)
- Debugger (step through code)
- Tests (isolate the problem)
- System understanding (how should this integrate?)

**Red Flags in AI-Generated Code**:
- Assumes paths/conditions that may not exist
- Doesn't handle errors
- Ignores edge cases
- Doesn't integrate with your actual systems
- Uses patterns not in your codebase

**When to Ask for Regeneration**:
- After you understand what's wrong
- With more specific feedback ("This assumes X, but we actually Y")
- Not: "This doesn't work"
- But: "This doesn't work because [root cause]; here's what should happen instead"

**Activities**:
- Take a piece of AI-generated code
- Deliberately break it (change assumptions)
- Practice debugging it back to working
- Document what you learned about the code
- Share debugging process with team

---

### Session 30: Organizational Dynamics & Information Loss
**Duration**: 1 hour
**Audience**: Engineering leads, PMs
**Objectives**:
- Understand how poor AI use creates organizational information loss
- Learn to set team standards that prevent mediocre output
- Establish team voice and communication excellence

**Key Concepts**:
- Teams without intention converge on bland, mediocre AI output
- AI default voice loses critical information
- Information loss in documentation = harder decisions = slower teams
- Team standards compound over time

**The Convergence Problem**:
- AI default is safe but vague
- Without intentional override, teams drift toward generic output
- Generic output could describe any system (not useful)
- Information needed for decisions disappears

**Red Flags of Information Loss**:
- Documentation sounds like it could apply to any system
- Requirements don't articulate trade-offs
- Roadmaps don't explain WHY items matter
- Architecture decisions hedge between options equally
- Team can't answer "why did we decide this?"

**Establishing Team Standards**:

1. **Voice standards**: What does clear, convicted communication look like?
2. **Rejection criteria**: What output does the team reject?
3. **Failure cases**: Concrete examples of what NOT to produce
4. **Ownership**: Someone is responsible for quality

**Practical Approach**:
- Review team's recent documentation together
- Discuss: Is this useful? What's missing?
- Build shared definition of "good"
- Make information loss visible (flag it in reviews)
- Own your communication standards

**Activities**:
- Audit team's recent documentation
- Identify information loss patterns
- Build team voice and conviction standards
- Review and refine existing docs against standards

---

### Session 31: PM-Specific: Using AI Intentionally in Product Work
**Duration**: 1 hour
**Audience**: Product managers
**Objectives**:
- Learn how PM clarity directly enables engineering excellence
- Apply AI responsibly in product requirements
- Measure impact on engineering velocity and quality

**Prerequisites**: Sessions 3-5, 24-26
**Content**: Product-specific intent and standards

**Key Concepts**:
- PM outputs directly shape engineering priorities
- Vague PM documents cascade into wasted engineering effort
- Information loss in PM work compounds exponentially
- PMs control a huge lever for engineering team quality

**PM Use Cases That Need Intention**:
- Product Requirements Documents (PRDs)
- Roadmap communication
- Feature specifications
- Competitive analysis
- Strategic direction
- Release notes

**PM-Specific Failure Cases**:
- Requirement: "I built feature X, but product didn't actually want it that way"
- Roadmap: "Sounds good but doesn't guide anyone"
- Strategy: "Could describe any company in any industry"
- Analysis: "Generic observations, not actionable"

**Good PM AI Use Pattern**:
1. **Write intent first** (usually 3-5 sentences answering):
   - What's the core problem?
   - Who wins if we solve this?
   - What decision/action does this enable?
   - What trade-offs are we accepting?
   - What are we explicitly NOT doing?

2. **Use AI for iteration**, not generation:
   - Add structure
   - Refine language
   - Add examples
   - Catch gaps

3. **Build PM failure cases**: Catalog specs that led to miscommunication

4. **Establish PM standards**: What does good look like?

**Cross-Functional Standard Setting**:
- Have engineering and PM together define: "What does good requirement look like?"
- Have design, engineering, PM align on: "What makes clear roadmap?"
- Shared language reduces friction

**Activities**:
- Review last quarter's PRDs
- Identify specifications that led to iteration/rework
- Rebuild 1-2 using intent-first approach
- Establish PM documentation standards
- Share standards with engineering team

---

### Session 32: Professional Responsibility & Ownership
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Establish professional standards for AI-assisted work
- Understand responsibility cannot be delegated to AI
- Build team culture of quality and ownership

**Key Concepts**:
- You own every line you ship, regardless of where it came from
- Professional differentiation comes from expertise + responsibility
- If you can only prompt, you've lost leverage
- Ownership mindset drives success with AI tools

**The Standard**:
- You must be able to explain every line you ship
- You must be able to defend your design decisions
- You must be able to debug when things break
- You must understand how your code fits in the system

**Antipatterns to Avoid**:
- "The AI wrote it" (doesn't matter; you shipped it)
- Prompt → copy-paste → ship (without understanding)
- Accepting AI output because it looks good (verify it actually works)
- Using AI as excuse to skip design thinking
- Blaming AI when your code fails

**Building a Culture of Responsibility**:
1. **Code review**: Verify understanding, not just correctness
2. **Design discussions**: Own your architectural decisions
3. **Debugging**: Learn from failures, build expertise
4. **Learning**: Invest time in understanding your tools
5. **Standards**: Maintain discipline even when AI makes it fast

**The Leverage Question**:
- If anyone can prompt an LLM, what makes you valuable?
- Answer: Your ability to specify, review, debug, and own quality
- That's where professionals differ from people who can prompt

**Activities**:
- Reflect: Can you explain every line in your recent PRs?
- Review: What code can you confidently defend?
- Debug: Walk through a system problem you encountered
- Document: What do you understand about your systems?

---

### Session 33: Building Team Standards & Continuous Improvement
**Duration**: 1 hour
**Audience**: All engineers, especially leads
**Objectives**:
- Establish team-wide standards for AI-assisted work
- Build systematic improvement process
- Document and iterate on standards over time

**Prerequisites**: Sessions 24-26, 30
**Content**: Team standardization and governance

**Elements of Strong Standards**:

1. **Code Quality Standards**:
   - Testing requirements for AI-generated code
   - Code review depth for different risk levels
   - Edge case coverage expectations
   - Security and performance requirements

2. **Communication Standards**:
   - What makes a good spec?
   - What makes a good architecture decision?
   - What makes a good code review comment?
   - Team voice and conviction standards

3. **Process Standards**:
   - How do we use AI in our workflow?
   - What prompting techniques do we use?
   - How do we handle iteration?
   - When do we debug vs. regenerate?

4. **Tool Standards**:
   - Which AI tools does team use?
   - Which Cursor commands are standard?
   - Which MCPs do we maintain?
   - How do we stay current?

**Building Standards**:
1. Start with team's pain points
2. Catalog what works and what doesn't
3. Document standards clearly
4. Teach and reinforce them
5. Iterate based on learning

**Documenting Standards**:
- Link to session content (what it is)
- Show examples (what it looks like)
- Explain why (what problem it solves)
- Include non-examples (what to avoid)
- Make them discoverable (how to find them)

**Continuous Improvement**:
- Regular retros on AI-assisted work
- Document lessons learned
- Update standards quarterly
- Share across team
- Celebrate improvements

**Activities**:
- Facilitate team discussion: What standards matter?
- Draft 3-5 core standards
- Document with examples
- Share and get feedback
- Plan quarterly refinement process

---

### Session 34: Learning & Psychological Safety
**Duration**: 1 hour
**Audience**: Engineering leaders
**Objectives**:
- Create environment for learning AI tools effectively
- Understand the learning curve and set realistic expectations
- Enable team to experiment transparently

**Key Concepts**:
- Takes time and play to develop AI tool proficiency
- Continuous learning is professional responsibility
- Skepticism often reflects insufficient experimentation
- Psychological safety enables learning during transition

**The Learning Curve**:
- Week 1-2: Basic usage, discovering use cases
- Week 3-4: Aha moments, understanding capabilities/limitations
- Week 5+: Ongoing experimentation, new tools constantly emerging
- Staying current is active practice, not passive knowledge

**Common Skepticism**:
- Haven't tried it yet
- Tried it briefly, didn't see the value
- Concerned about theory/ethics/energy/etc.
- These are often valid concerns, but incomplete picture

**Creating Psychological Safety**:
1. **Model learning**: "I'm experimenting with this; here's what I found"
2. **Celebrate experiments**: "Nice that you tried this approach"
3. **Normalize failure**: "What did you learn when that didn't work?"
4. **Share transparently**: "Here's what's working and what isn't"
5. **Give time**: "This is a multi-month transition, not overnight"

**Team Learning Practices**:
- Weekly "what are you experimenting with?" discussions
- Office hours for questions
- Shared experiments (pair programming with AI)
- Documentation of learnings
- Rotation through new tools/techniques

**First Principles Thinking**:
- Understand what LLMs are trained on (affects baseline quality)
- Understand model limitations (sets expectations)
- Understand tradeoffs (reasoning about when to use)
- This helps set realistic expectations

**Activities**:
- Have each team member experiment with one new technique this week
- Share learnings in next meeting
- Discuss what surprised them
- Identify what to adopt as team practice

---

### Session 35: Capstone - Building Your Integrated Workflow
**Duration**: 1 hour
**Audience**: All engineers
**Objectives**:
- Integrate all techniques into coherent workflow
- Apply to real team task
- Document and share learnings

**Prerequisites**: All previous sessions
**Content**: Integration and application

**Building Your Workflow**:

Choose one recurring task your team does. Build complete workflow combining:

1. **Specification** (Sessions 3-4)
   - Clear intent
   - Structured requirements
   - Failure cases
   - Success criteria

2. **Prompting** (Sessions 6-15)
   - Right techniques for the task
   - Proper scaffolding
   - Evaluation criteria

3. **Tools** (Sessions 16-18)
   - Cursor commands
   - MCPs if applicable
   - Automation opportunities

4. **Quality** (Sessions 20-23)
   - Testing strategy
   - Code review process
   - Evaluation criteria

5. **Communication** (Sessions 24-26)
   - Clear intent
   - Voice and conviction
   - Documentation standards

**The Workflow Document Should Include**:
- What problem does this solve?
- How do we specify work?
- What prompts/commands do we use?
- What testing/review happens?
- How do we iterate?
- What standards apply?
- How do we measure success?

**Implementation Process**:
1. Run workflow on real task
2. Measure: velocity, quality, team confidence
3. Gather feedback from team
4. Refine based on learning
5. Share with broader team

**Scaling Across Team**:
- Document workflow
- Train team members
- Iterate based on feedback
- Build into standard practices
- Update as tools/techniques evolve

**Activities**:
- Choose your team's highest-value workflow
- Document complete workflow
- Run on 3-5 actual tasks
- Measure: time, quality, rework
- Present results to team
- Plan adoption and scaling

---

## **Measurement & Success Metrics**

Track these across your training program:

**Velocity Metrics**:
- Tasks completed per sprint
- Time to code review
- Time from task start to merge
- Cycle time improvement

**Quality Metrics**:
- Defect escape rate (bugs after merge)
- Code review iterations required
- Test coverage
- Production incidents related to AI-generated code

**Efficiency Metrics**:
- Prompt iterations needed per task
- Rework rate (specs vs. actual implementation)
- Manual debugging time
- Context switching / interruptions

**Adoption Metrics**:
- % of code using AI assistance
- % of docs using AI assistance
- Tool proficiency level (self-assessed)
- Standards compliance rate

**Learning Metrics**:
- Team confidence (survey)
- Experimentation rate (new techniques tried)
- Knowledge sharing (peer learning)
- Skill assessment (can explain concepts)

---

## **Session Scheduling Suggestions**

- **Foundation** (Sessions 1-5): Run first, back-to-back or close together
- **Prompting** (Sessions 6-15): Can run in any order after foundations
- **Tools** (Sessions 16-18): Can run in parallel with prompting sessions
- **Quality** (Sessions 20-23): Should follow prompting sessions
- **Integration** (Sessions 24-35): After team has foundational knowledge

**Recommended Pace**:
- 1-2 sessions per week
- 12-18 weeks to complete full program
- Allows time for practice between sessions
- Enables real-world application and learning

---

## **Appendix: Quick Reference by Role**

**For All Engineers**: Sessions 1-15, 20-26, 30, 32, 34

**For Junior Engineers**: Add 29 (debugging)

**For Senior Engineers**: Add 29, 31, 33 (leadership and standards)

**For Engineering Leads**: Add 20, 30, 31, 33, 34 (team dynamics and governance)

**For Product Managers**: Sessions 3-5, 24-26, 31 (intent, communication, PM-specific use)

**For ML/AI Specialists**: Add 17-18 and deep dive on any advanced prompting sessions
