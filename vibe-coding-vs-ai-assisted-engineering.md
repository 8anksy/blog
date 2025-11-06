# Beyond Vibe Coding: High-Level Topics & Core Ideas

## **1. Vibe Coding vs. AI-Assisted Engineering**
- Vibe coding prioritizes speed and exploration over correctness and maintainability
- AI-assisted engineering keeps the human engineer firmly in control and responsible for quality
- The distinction is critical to not devalue the discipline of engineering
- Vibe coding is useful for prototypes and MVPs, but shouldn't go directly to production
- There's a spectrum between vibe coding and traditional software engineering

### Key Definitions:
- **Vibe Coding**: Fully giving into creative flow with AI, high-level prompting, accepting AI suggestions without deep review, rapid iterative experimentation
- **AI-Assisted Engineering**: AI as a powerful collaborator and force multiplier, but NOT a replacement for engineering principles; human remains responsible for architecture, review, and quality

### Important Distinction:
The speaker emphasizes that vibe coding and AI-assisted engineering are NOT the same thing. This matters because it affects how junior engineers perceive software engineering discipline. Vibe coding shows value in rapid ideation, but production-ready software requires different rigor.

**Research Connection**: Andrej Karpathy's original definition of vibe coding is referenced as foundational to this conversation.

## **2. The 70% Problem**
- LLMs can produce roughly 70% of a working application quickly but struggle with the last 30%
- The final 30% includes hidden maintainability costs, security vulnerabilities, and edge cases
- More experienced engineers can finish the last 30% easier; junior engineers get stuck
- Spec-driven development helps derisk LLM usage and provides higher quality outcomes
- A vibe-coded prototype needs to be rewritten with production quality in mind

### What the Last 30% Includes:
- **Two Steps Back Pattern**: LLM completely rewrites UI or functionality with a single prompt
- **Hidden Maintainability Costs**: Lack of specificity delegates responsibility back to LLM, leading to diminishing returns
- **Security Vulnerabilities**: API key leaks, XSS issues, and other OWASP vulnerabilities from not thinking holistically about the problem
- **Edge Cases**: Scenarios not covered in typical code samples from training data

### Key Pattern Observed:
- Junior engineers and interns: Stuck in reprompting loop, lacking debugging skills to understand root causes
- Experienced engineers: Can debug manually, understand system connections, recognize patterns

### Research Note:
Addy Osmani published extensively about the 70% Problem approximately 6 months before this podcast (circa April 2024). This is a core concept worth researching separately for deeper understanding.

## **3. Critical Thinking & Professional Responsibility**
- Understanding what the model does before approving changes is essential
- Engineers should be able to explain and defend every line of code they ship
- Reading the thinking logs of AI models ensures full comprehension
- Without deep understanding, developers become hopelessly dependent on AI tools
- Professional engineers know how things work and can fix problems when models fail

### Addy's Personal Practice:
- Uses tools like Claude (with thinking logs visible)
- Reads through the thinking process, expands explanations, reviews code before pull requests
- Values understanding the model's reasoning, decisions, and what it generated
- Will manually debug when LLM solutions don't work (recent example: code looked correct but didn't function properly)

### The Professional Standard:
The difference between a software engineer worth their salary and one who isn't: ability to prompt effectively is not unique (anyone can do it). Professional differentiation comes from:
- Rolling up sleeves and manually debugging when models fail
- Explaining code coherently to others
- Having the expertise to understand architecture and systems

### Warning Sign:
If you can only prompt and prompt, anyone (including college students) can do that. You're losing leverage if you don't understand the underlying code.

## **4. Best Practices for AI-Assisted Development**
- Spec-driven development with clear plans produces higher quality outcomes
- Testing is a great way to derisk LLM usage in coding
- Break tasks into small, verifiable chunks rather than throwing everything at once
- Small contextual prompts work better than large unstructured prompts
- Keep the browser in the loop (tools like Chrome DevTools MCP help AI see what's rendering)

### Spec-Driven Development Approach:
1. Have a very clear plan of what you want to build
2. Write out actual expectations and requirements
3. Delegate to AI with explicit specifications, not vague vibes
4. This produces much higher quality outcomes than giving the LLM responsibility for architecture

### Testing Strategy:
- Tests prove things are working with AI-generated code
- Tests clarify what went wrong when things break
- Keeps projects "green" throughout development
- Significantly reduces risk of undetected issues reaching production

### Task Decomposition (Like Sprint Planning):
- Adopt project manager mindset
- Break work into small, verifiable chunks
- Avoid throwing entire requirements at LLM at once
- Clear expectations and preparation to iterate
- Reduces risk of context loss and compounding errors

### Tools & Monitoring:
- Chrome DevTools MCP: Gives LLM "eyes" to see what's rendering in browser
- Detects warnings and errors in console
- Improves feedback loop by showing AI what's actually broken vs. assumptions
- Related solutions help evolve workflows through tool integration (MCPs)

## **5. The Human Bottleneck & Code Review**
- Increased velocity from AI coding makes human review the new bottleneck
- Having AI both write and review code is a slippery slope
- Code review best practices are still evolving with AI tools
- Human oversight remains central to preventing tech debt and quality issues
- Teams must maintain psychological safety for learning and experimentation

### The Bottleneck Effect:
- Studies show: When velocity of code and improvements increases AND human oversight is applied, human review becomes the bottleneck
- Teams seeing more PRs than ever but unsure who reviews them
- Some teams are at least caring about quality dimension and doing human review themselves

### The AI Review Problem:
- If AI writes the code and you haven't studied it carefully, and AI also reviews the code, how sure are you about what's landing?
- This creates a dangerous feedback loop without actual human understanding
- The slippery slope: Skip thorough reading → AI suggests changes → AI reviews its own suggestions → Ship unreviewed code

### External Contributor Issue:
- External contributors using LLMs submit code with good intentions but put massive load on maintainers
- Maintainers (like Michał Hashimoto) have called this out on social media
- The problem: increased volume without corresponding increase in quality or understandability

### The Professional Standard for Reviews:
- Teams benefit from people who are thorough and never let things slip
- Senior engineers call out even the smallest issues
- This becomes leverage for seniors, not busy work, when managing multiple agents

## **6. New Workflows & Emerging Patterns**
- Asynchronous background coding agents are emerging (Jude, CodeEx, GitHub)
- Parallel agents working simultaneously is a new capability never before possible
- Vibe designing (Figma MCP) allows designers to turn visions into functional prototypes
- Trio programming (junior + senior + AI) could become a new mentoring model
- Forward-deployed engineers embedded with customers could blur PM/developer roles

### Asynchronous Background Agents:
- Multiple teams (Jude, CodeEx, GitHub, Linear agents) exploring this pattern
- Ability to delegate parts of backlog for asynchronous implementation
- Currently work well for: writing/updating tests, library version migrations, smaller changes
- Jury still out on larger, complex feature implementation
- Key requirement: merge conflict-free and easily human verifiable

### Parallel Agent Execution (NEW CAPABILITY):
- This is historically unprecedented - developers have never been able to do this before
- Differs from context-switching between problems (traditional developer workflow)
- Differs from teams working on different tasks (traditional team structure)
- One developer orchestrating multiple agents completing work concurrently
- Challenge: Developers have finite attention; managing many agents requires discipline
- Better analog: How senior engineers currently work with teams of juniors and interns

### Vibe Designing (Figma Integration):
- Real-world example: Shopify design team - every designer has Cursor
- Workflow: Create Figma design → Ask AI to implement it → Show interactive prototype to engineers
- Major difference from old flow: moving from static designs to interactive, functional prototypes
- Designer upskilling: Designers learning to code/work in developer tools
- Not saying "ship it" - using it as starting point for engineering conversation

### Trio Programming:
- Emerging mentoring model: Junior + Senior + AI
- Senior's role: Ask junior to explain AI-generated code, walk through system connections
- Uses AI as additional tool while building junior confidence and systems understanding
- Maintains human mentorship and knowledge transfer

### Forward-Deployed Engineers:
**Addy's Cautious Optimism:**
- Developers embedded with customers who can build features rapidly with AI
- They feed back requirements to engineering teams
- Blur lines between developer, PM, and designer roles
- "I'm interested to see where that goes" - still exploratory, not proven yet
- Potential organizational implications still being worked out

### The Conductor/Orchestration Problem:
- Even with multiple agents, human attention is the bottleneck
- Some demos show 20 terminals with multiple agents - looks impressive but impractical
- Key question: What's the right UI/surface for managing multiple concurrent agents?
- Answer likely depends on developer's code review and diligence capacity

## **7. Context & Knowledge Systems**
- Institutional knowledge matters more than single prompts
- Real product development depends on accumulated context (bug history, customer requests, team roadmap)
- LLMs don't see organizational context like Linear agents do
- Context engineering is about optimizing the context window for better outcomes
- Prompt engineering and context engineering are both critical skills

### The Context Gap Problem:
- Vibe coding limitation: "Every prompt stems from what you tell it, not what your team already knows"
- Real product development is accumulated context over time
- Every bug has a history; every feature connects to customer requests; every PR fits team roadmap
- Short prompts will not capture this additional organizational context

### Linear Agents as Example:
- Agents live inside development system where actual work happens
- They see: issues blocking sprint, related PRs, project goals, team discussions
- Agents see entire org context: bug reports from support, design specs from PM, architecture notes from tech lead
- Not improvising from prompt alone; using same context the team uses

### Context Engineering Skills:
- Making sure right descriptions, details, files, examples are provided
- Optimizing context window to increase chances of higher quality outcomes
- Project-specific content that LLM may not have in training data is critical
- Google teams spend significant time thinking about this explicitly

### Training Data Limitations:
- Training data comes from permissively licensed code on GitHub and open web
- Patterns reflect "lowest common denominator" of what works
- Not necessarily secure, high-performance, or accessible implementations
- This means baseline quality expectations need to be lower when using LLMs

## **8. Expertise & Skill Leverage**
- Greater software engineering expertise leads to better results with LLMs
- AI raises the floor for new engineers but also raises the ceiling for seniors
- Seniors become more valuable because they can write specs, decompose work, and review effectively
- The last 30% becomes leverage for seniors, not busy work
- Building expertise in AI tools takes time, experimentation, and team sharing

### The Expertise Multiplier Effect:
- More expertise in software engineering = better outcomes with LLMs
- New engineers/juniors: maybe haven't experienced traditional best practices yet
  - E.g., code you commit should be explainable to someone else
  - They don't have debugging skills for when things go wrong
  - They lack pattern recognition for architectural issues
- Experienced engineers: know what good looks like, understand tradeoffs

### Raising the Floor AND Ceiling:
- **Floor Raised**: New engineers can produce working code faster than before
- **Ceiling Raised**: Seniors can move even faster and with more leverage
- Not a leveling effect - actually increases gap between junior and senior output

### The Value of Seniors in AI Era:
- Can write clear specifications and decompose complex work
- Can review code effectively and spot issues quickly
- Understand systems architecture and dependencies
- Debug when LLMs fail (which they do)
- Know when to use AI vs. when to solve manually
- Can mentor others on AI tools and best practices

### The Last 30% as Leverage:
- For seniors: The hard part (finishing complex work, handling edge cases) = leverage
- Not busy work, but actually valuable problem-solving
- Parallels their current role managing junior engineers and handling complex problems

### Building AI Proficiency:
- Requires time, deliberate practice, and experimentation
- Addy experiments with new models/tools every week
- Encourages teams to share learnings transparently
- Creates psychological safety for learning together during period of change

## **9. Educational & Role Evolution**
- Junior engineers and new grads need to build critical thinking and problem-solving skills
- Code comprehension and systems thinking remain timeless practices
- PM and EM roles are evolving toward problem framing, metrics, evals, and safety reviews
- Taste in product engineering will differentiate teams (since anyone can use prompts)
- Education systems need to reconsider what to teach about AI-assisted development

### Skills That Won't Go Away:
- Understanding how the code works (not just that it works)
- Problem-solving and debugging abilities
- Critical thinking and systems design mindset
- Code comprehension - understanding how pieces connect
- "This diligence required to actually read the code, understand the system, understand how all of those pieces connect together... doesn't necessarily go away"

### The Reprompting Trap for Juniors:
- When stuck on the last 30%, juniors keep reprompting without understanding root causes
- Don't have debugging skills to understand where issues are
- Problem: False confidence from quickly generated code, but inability to fix when it breaks

### PM Role Evolution:
- Moving away from: Detailed requirement writing for developers
- Moving toward: Problem framing, metrics, policies for agents
- New considerations: How to structure work for AI agents, what success looks like

### EM (Engineering Manager) Role Evolution:
- Moving toward: Evals and safety reviews
- Responsibility: Enabling teams to work with AI confidently
- More guidance on what's possible/not possible with current tools
- Mentoring on AI-assisted development practices

### Product Differentiation Through Taste:
- Since anyone can prompt an LLM to build similar functionality, taste becomes differentiator
- Taste = Product intuition, design decisions, knowing what users actually want
- Similar to how early web/mobile companies differentiated - functionality was leveled, taste wasn't

### Education System Questions:
- Should high school/college teach prompt and context engineering?
- How to teach systems thinking and design when AI is doing code generation?
- What's the new curriculum for AI-native engineers?
- How do we maintain rigor in fundamental CS concepts?

### The Armen Ronacher Observation:
- Engineers firmly in control of work: seeing more success with AI
- Engineers assigned tickets, feeling no control: more anxious about AI
- Key difference: Ownership mindset and confidence in ability to progress independently

## **10. Learning & Psychological Safety**
- It takes time and playing around to develop proficiency with AI tools
- Skeptics often haven't tried the tools or haven't given them enough time
- Teams benefit from shared learning and transparent experimentation
- Psychological safety enables teams to learn together through periods of change
- Continuous learning is a professional responsibility

### Common Skepticism:
- Plenty of engineers skeptical about LLMs (theory, energy footprint, ethics, etc.)
- But most skeptics either: haven't tried it OR haven't given it sufficient time
- Takes playing around and multiple attempts to discover where it actually helps
- Doesn't work everywhere; mistakes happen; it screws up sometimes
- But discovering use cases requires patient exploration, not dismissal

### The Learning Curve:
- Takes time to learn these tools properly - can't just assume you understand them
- Ongoing "aha moments" even for experienced practitioners
- New models, new tools, new platforms constantly emerging
- Addy personally experiments with new tools every week
- Staying current is an active practice, not passive knowledge

### Team Learning & Experimentation:
- Encourages transparent sharing: "How are things going? Any insights worth sharing?"
- Teams that see leadership open to learning create psychological safety
- Psychological safety sets teams up for success during periods of change
- This is especially important during AI transition period

### First Principles Thinking:
- Going back to fundamentals helps a lot with understanding these tools
- Understanding what LLMs are trained on (permissively licensed code from GitHub/web)
- Understanding model limitations and reasoning about outputs
- Helps set more realistic expectations about what to expect from them

### Comparison to Stack Overflow Era:
- 10-15 years ago: Developers copied regex solutions from Stack Overflow
- Similar pattern emerging now: Accepting AI code without understanding it
- But the scale is much larger - copy-pasting one regex vs. entire features
- Same risk: insecure code, edge cases, bugs discovered later
- Professional responsibility same then as now: understand what you ship

### Institutional Knowledge and Professional Integrity:
- You take on responsibility for code you commit, regardless of where it came from
- Whether you wrote it, copied it, or AI generated it - you own it
- If you can't explain it or maintain it, you shouldn't commit it
- "If you don't know how it works, why do we need you? Anyone can prompt another LLM"
