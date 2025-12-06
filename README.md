# A22: A Functional, Temporal Language for Agentic Systems

**Whitepaper by the A22 Foundation**

---

## Abstract

Agentic systems are rapidly becoming part of everyday workflows, but defining agent behavior remains unnecessarily complex. Most approaches treat agents as imperative programs with mutable state, making behavior unpredictable and difficult to reason about.

**A22 introduces a fundamentally different approach**: a functional, immutable, temporal language that describes agentic systems as **pure dataflow graphs over time**. By modeling everything as events flowing through an immutable context, A22 makes agent behavior deterministic, testable, and comprehensible.

A22 is builder-friendly first—using natural language syntax that reads like structured thought. It's vendor-neutral, execution-agnostic, and ensures the same A22 program behaves identically across implementations.

This whitepaper outlines the principles, architecture, and philosophy behind A22, showing how functional programming principles make agentic systems accessible to everyone.

---

## 1. Introduction: The Problem with Imperative Agents

Current agentic systems face fundamental challenges:

### 1.1 Mutable State Creates Unpredictability

Traditional agent frameworks use mutable state—variables that change, databases that update, memory that overwrites. This makes agents:
- **Non-deterministic**: Same inputs can produce different outputs
- **Difficult to debug**: State mutations happen invisibly
- **Hard to test**: Cannot replay exact scenarios
- **Impossible to reason about**: No clear causality

### 1.2 Imperative Code Is Too Technical

Most agent tools require programming:
- Complex orchestration logic
- State management boilerplate
- Error handling gymnastics
- Framework-specific APIs

This excludes non-technical builders who understand their domain deeply but shouldn't need to code.

### 1.3 Vendor Lock-In

Tightly coupled to specific:
- Execution environments
- Cloud providers
- Model APIs
- Tool ecosystems

**A22 solves these problems by treating agent behavior as functional, immutable, temporal dataflow.**

---

## 2. The A22 Foundation: Five Primitives

A22 reduces all agentic behavior to **five irreducible primitives**:

### 2.1 Event

**Events are the only thing that "happens" in A22.**

```
event = {
  type: symbol,      # e.g., "message.incoming"
  time: timestamp,   # When it occurred
  data: map          # Immutable payload
}
```

Everything in the system is an event:
- User messages → `message.incoming`
- Tool calls → `tool.request`, `tool.response`
- Agent outputs → `agent.thinking`, `agent.output`
- Policy checks → `policy.validated`
- Human decisions → `hil.request`, `hil.response`

Events are:
- **Immutable** — Never change after creation
- **Timestamped** — Always know when they occurred
- **Causally ordered** — Clear sequence of what happened

### 2.2 Context

**Context is the complete, immutable history of all events.**

```
context_t = [event_0, event_1, event_2, ..., event_t]
```

Context is:
- **Append-only** — Events are only added, never removed or modified
- **Shared** — All components read from the same context
- **The source of truth** — Everything derives from events

**State is not stored—it's computed.**

Want to know the conversation history? Filter events where `type = "message.*"`.
Want the last 50 messages? Take the last 50 from that filter.
Want user preferences? Find `preference.updated` events and get the latest.

State becomes **views over immutable history**, not mutable variables.

### 2.3 Agent

**Agents are pure functions from context to events.**

```
agent(context, input) -> event
```

An agent:
- Reads only from context (cannot mutate)
- Takes input
- Produces a new event
- That event gets appended to context

Example:
```
agent("chatbot", context, "Hello")
  → event(type: "agent.output", data: "Hi there!")
```

Because agents are pure functions:
- **Deterministic** — Same context + input = same output
- **Testable** — Replay exact scenarios
- **Debuggable** — Trace every decision through events
- **Composable** — Combine agents predictably

### 2.4 Workflow

**Workflows are temporal DAGs (Directed Acyclic Graphs) of pure steps.**

```
step(context, inputs) -> event
```

Workflows orchestrate dataflow:
1. Step runs when inputs are available
2. Step produces event
3. Event appends to context
4. Next steps become eligible

Built-in constructs:
- `steps` — Sequential execution
- `parallel` — Concurrent execution
- `branch` — Conditional paths
- `loop` — Iterative refinement
- `human_in_loop` — Human decision points

All deterministic. All pure. All traceable through events.

### 2.5 Tool

**Tools are externalized side effects, pure from A22's perspective.**

```
tool_call(input) -> event
```

Tools may call external APIs, run code, access databases—but from A22's view, they're pure:
- Input → Tool execution → Output event
- The event is what enters the context
- Side effects happen "outside" the functional core

This separation:
- Keeps the agent model pure
- Makes testing possible (mock tool events)
- Preserves determinism
- Enables sandboxing and validation

---

## 3. The Runtime Model: Everything Is Events

### 3.1 The Event Loop

A22 runtime is a simple loop:

```
loop:
  1. Receive input or trigger
  2. Component(context, input) → new_event
  3. Validate via policies
  4. Append event to context
  5. Trigger next steps / workflows
```

**Everything is an event. Nothing mutates. Time flows forward.**

### 3.2 Example: Message Flow

```
Input: user.message("Hello")
  ↓
agent("chatbot", context, "Hello") → event(agent.output, "Hi there!")
  ↓
context' = context + [event]
  ↓
trigger response handlers
```

### 3.3 Context Evolution

```
context_0 = []
context_1 = [message.incoming]
context_2 = [message.incoming, agent.thinking]
context_3 = [message.incoming, agent.thinking, agent.output]
```

Want to see what happened? Look at the events.
Want to replay? Use the same context.
Want to debug? Trace the event chain.

### 3.4 State as Views

```
conversation = filter(context, type="message.*")
last_50 = take(conversation, 50)
preferences = latest(filter(context, type="preference.*"))
```

State isn't stored—it's **projected from events**.

This means:
- No state synchronization issues
- Perfect auditability
- Time-travel debugging possible
- Replay any scenario exactly

---

## 4. Natural Language Syntax: Builder-Friendly

A22 uses **indentation-based, natural language syntax** that reads like structured thought:

```a22
agent "research_assistant"
	can search, analyze, summarize
	use model: :gpt4
	use tools: [web_search]

	prompt :system
		"You are a research assistant."

	state :persistent
		backend :redis
		ttl 24h

	remembers
		queries: last 100
		findings: always

	when user.query
		-> .research_workflow
```

### 4.1 Design Principles

- **Intuitive** — Reads like describing behavior to another person
- **Indentation-based** — Like Python/YAML, visual structure
- **Natural verbs** — `can`, `use`, `do`, `has`, `when`
- **Symbols** — `:gpt4`, `:redis` for named references
- **References** — `.workflow_name` for cross-references
- **Minimal noise** — No braces, semicolons, or boilerplate

### 4.2 Complete Example

```a22
workflow "iterative_research"
	steps
		# Parallel search across sources
		parallel
			web = web_search query: input.topic
			papers = arxiv_search query: input.topic

		# Synthesize findings
		synthesis = agent "analyst"
			message: "Analyze and synthesize"
			context: [web.results, papers.results]

		# Quality loop
		loop max: 3
			quality = evaluate draft: synthesis

			when quality.score >8
				-> break

			synthesis = improve
				content: synthesis
				feedback: quality.feedback

		# Human approval gate
		approval = human_in_loop
			show: synthesis.content
			ask: "Approve for publication?"
			options: [approve, reject, revise]
			timeout: 1h

		branch approval
			when "approve" -> publish synthesis
			when "reject" -> archive synthesis
			when "revise" -> .revision_workflow

		return synthesis
```

This **reads naturally** but is **precisely defined** and **deterministically executable**.

---

## 5. Why Functional/Immutable/Temporal?

### 5.1 Determinism

**Same context + same input = same output.**

This is impossible with mutable state but guaranteed with pure functions over immutable events.

Benefits:
- Predictable behavior
- Reproducible bugs
- Reliable testing
- Trustworthy agents

### 5.2 Auditability

Every decision is an event in context. You can always answer:
- "Why did the agent do that?" → Trace the events
- "What was the state at that moment?" → Reconstruct from events
- "Can we replay this?" → Yes, use the same context

Critical for:
- Debugging
- Compliance
- Safety
- Trust

### 5.3 Testability

Pure functions are trivially testable:

```a22
test "agent responds correctly"
	given
		agent :chatbot
		context [message.incoming("Hello")]

	expect
		produces event.type "agent.output"
		event.content contains "Hi"
		completes within 5s
```

No mocks needed for the core logic—just events.

### 5.4 Composability

Pure functions compose cleanly:

```
workflow = agent_a → tool_b → agent_c
```

Each step is independent, testable, and predictable.

### 5.5 Scalability

Immutable data structures enable:
- **Parallelism** — No race conditions
- **Distribution** — Share context read-only
- **Caching** — Events never change
- **Replay** — Reprocess any event stream

---

## 6. Specification vs. Implementation

A22 is a **specification**, not an implementation.

### 6.1 The A22 Specification

Defines:
- Five primitives (Event, Context, Agent, Workflow, Tool)
- Natural language syntax
- Semantic meaning of constructs
- Determinism requirements
- Event schemas
- Validation rules

Does **not** define:
- How context is stored
- Which models are used
- How tools execute
- Infrastructure requirements
- Runtime optimizations

### 6.2 Implementations

Many valid implementations:
- Cloud runtimes (serverless, containerized)
- Edge devices
- Desktop applications
- Embedded systems
- In-browser interpreters

All must:
- Produce the same events for the same inputs
- Maintain append-only context
- Enforce pure function semantics
- Validate against policies

But may differ in:
- Performance characteristics
- Storage backends
- Model integrations
- Tool ecosystems
- UI/UX

### 6.3 Guarantees

**Same A22 program → same behavior** across implementations.

This ensures:
- No vendor lock-in
- Portable agent definitions
- Long-term stability
- Innovation at execution layer without breaking programs

---

## 7. Builder Experience: Technical and Non-Technical

### 7.1 For Non-Technical Builders

A22 is **configuration, not code**:

```a22
agent "support_bot"
	can chat, retrieve_docs, escalate
	use model: :gpt4

	prompt :system
		"You are a helpful support agent."

	when user.question
		-> .support_workflow
```

- No variables to manage
- No state mutations
- No error handling boilerplate
- Just describe behavior

### 7.2 For Developers

Full extensibility:

```a22
tool "custom_analyzer"
	runtime :python
	handler "analyzers/sentiment.py"

	input
		text: string
		threshold: number

	output
		sentiment: string
		confidence: number

	sandbox
		timeout: 10s
		memory: 256mb
		network: none

	validates
		text
			max_length: 10000
			deny_patterns: ["<script"]
```

- Custom tools in any language
- Full sandbox control
- Validation rules
- Security policies
- Advanced workflows

### 7.3 Visual Mental Model

A22 maps naturally to diagrams:

```
user.message
    ↓
agent "chatbot"
    ↓
agent.output event
    ↓
context (append)
    ↓
trigger next handlers
```

This **visual clarity** helps builders:
- Understand the system at a glance
- Iterate confidently
- Debug effectively
- Explain to others

---

## 8. Security and Safety

Functional/immutable architecture enables strong safety:

### 8.1 Policies as Event Validators

Policies check events **before** they append to context:

```a22
policy :safe_mode
	allow
		tools [web_search, email_send]
		capabilities [chat, search]

	deny
		tools [system_commands]
		data [user_credentials]

	limits
		max_tokens: 10000
		max_execution_time: 60s
```

Violation → `policy.violation` event, operation halts.

### 8.2 Tool Sandboxing

Every tool execution is sandboxed:

```a22
tool "execute_code"
	sandbox
		timeout: 10s
		memory: 256mb
		network: none
		filesystem: readonly ["/tmp"]
```

### 8.3 Audit Trail

Every action is an event. Complete audit trail for free:

```
[
  {type: "message.incoming", time: "...", data: "..."},
  {type: "policy.checked", time: "...", data: {...}},
  {type: "agent.thinking", time: "...", data: "..."},
  {type: "tool.request", time: "...", data: {...}},
  {type: "tool.response", time: "...", data: {...}},
  {type: "agent.output", time: "...", data: "..."}
]
```

Perfect for:
- Compliance (GDPR, SOC2, etc.)
- Security investigations
- Quality assurance
- Debugging

### 8.4 Input Validation

Built-in validation for all tool inputs:

```a22
tool "send_email"
	validates
		to
			pattern: "^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\\.[a-zA-Z]{2,}$"
		body
			max_length: 10000
			deny_patterns: ["<script", "javascript:"]
```

---

## 9. Human-in-the-Loop: Natural Integration

HIL is just another event in the flow:

```a22
workflow "content_approval"
	steps
		draft = generate_article topic: input.topic

		approval = human_in_loop
			show: draft.content
			ask: "Approve for publishing?"
			options: [approve, reject, edit]
			timeout: 2h
			default: reject

		branch approval
			when "approve" -> publish draft
			when "reject" -> notify_rejection
			when "edit" -> .edit_workflow
```

Runtime emits:
1. `hil.request` event
2. Workflow pauses (waits for context update)
3. Human provides input
4. `hil.response` event
5. Workflow continues

Same pure, event-driven model. No special cases.

---

## 10. Why A22 Matters Now

### 10.1 The Agentic Explosion

Agents are everywhere:
- Customer support
- Research assistants
- Code generation
- Data analysis
- Creative work

But current tools are:
- Too technical for domain experts
- Too imperative for reliability
- Too vendor-locked for portability
- Too stateful for reasoning

### 10.2 A22 Provides

**For Builders:**
- Natural, readable syntax
- Predictable behavior
- No programming required
- Visual mental model
- Fast iteration

**For Developers:**
- Deterministic execution
- Easy testing
- Clear debugging
- Composable components
- Strong safety guarantees

**For Organizations:**
- Vendor neutrality
- Audit trails
- Compliance support
- Long-term stability
- No lock-in

### 10.3 The Functional Advantage

Functional programming has proven itself in:
- Databases (SQL, Datalog)
- Infrastructure (Terraform, Nix)
- Data pipelines (Spark, Flink)
- Web frameworks (React, Elm)

A22 brings these benefits to agentic systems:
- **Immutability** → Predictability
- **Pure functions** → Testability
- **Events** → Auditability
- **Temporal dataflow** → Debuggability

---

## 11. Governance & Community

### 11.1 The A22 Foundation

The Foundation is a **neutral steward**, not a vendor.

Responsibilities:
- Maintain the specification
- Ensure clarity and minimalism
- Guard against complexity creep
- Support the community
- **Never** control implementations

The Foundation owns the **language**, not the **infrastructure**.

### 11.2 A22IP: Improvement Proposals

Community-driven evolution via A22IPs (A22 Improvement Proposals).

Proposals must:
- Solve real builder needs
- Maintain determinism
- Preserve simplicity
- Keep the functional model intact
- Show backward compatibility path

The community—especially non-technical builders—drives decisions.

### 11.3 Versioning

Semantic versioning:

- **0.x** — Experimental (current)
  - Exploration, refinement, learning

- **1.0** — Stable Core
  - Foundational, long-term specification

- **1.x** — Incremental Improvements
  - Backward-compatible enhancements via A22IPs

Stability signals trust.

---

## 12. Roadmap

### Phase 1: Core Specification ✓ (Current)
- Five primitives
- Natural language syntax
- Event model
- Basic runtime semantics

### Phase 2: Builder Tooling
- Visual editors
- Syntax highlighting
- Documentation
- Examples and templates
- Tutorials for non-technical users

### Phase 3: Reference Runtime
- Open-source implementation
- Demonstrates specification compliance
- Testing framework
- Debugging tools

### Phase 4: Ecosystem Growth
- Community implementations
- Tool libraries
- Model integrations
- Platform connectors
- Conformance testing

### Phase 5: Production Hardening
- Security best practices
- Performance guidelines
- Scaling patterns
- Compliance frameworks
- Enterprise features

### Phase 6: Advanced Patterns
- Multi-agent systems
- Distributed workflows
- Streaming events
- Real-time feedback
- Complex orchestrations

### Phase 7: Long-Term Stewardship
- Maintain stability
- Guard simplicity
- Support community
- Ensure neutrality

---

## 13. Comparison to Existing Approaches

### 13.1 vs. LangChain / LlamaIndex

**Their Approach:**
- Python-based imperative code
- Complex abstractions
- Mutable state management
- Framework-specific patterns

**A22:**
- Declarative, functional specification
- Pure dataflow over events
- No mutable state
- Implementation-agnostic

### 13.2 vs. AutoGPT / BabyAGI

**Their Approach:**
- Loop-based agents
- Dynamic tool selection
- Ad-hoc memory management

**A22:**
- Structured workflows
- Explicit tool declarations
- Immutable event history as memory

### 13.3 vs. Terraform / Kubernetes

**Their Approach:**
- Infrastructure/container configuration
- Eventual consistency
- State reconciliation

**A22:**
- Behavioral configuration for agents
- Immediate consistency (events)
- Append-only context (no reconciliation needed)

### 13.4 Unique Position

A22 is the **only** functional, immutable, temporal language specifically designed for agentic systems with:
- Natural syntax for non-technical builders
- Pure function semantics for developers
- Event-sourced architecture for auditability
- Vendor-neutral specification

---

## 14. Design Principles (Summary)

1. **Functional** — Pure functions, no side effects in core logic
2. **Immutable** — Append-only events, never mutate
3. **Temporal** — Context evolves through time
4. **Declarative** — Describe intent, not execution
5. **Natural** — Reads like structured thought
6. **Safe** — Policies, sandboxing, validation
7. **Portable** — Vendor-neutral specification
8. **Deterministic** — Same input → same output
9. **Composable** — Build complex from simple
10. **Auditable** — Every action is an event

---

## 15. Conclusion

A22 represents a fundamental rethinking of how we define agentic systems.

By applying **functional programming principles**—pure functions, immutable data, event sourcing—to agent behavior, we achieve:
- **Determinism** previously impossible with stateful agents
- **Auditability** critical for trust and compliance
- **Testability** essential for reliability
- **Composability** needed for complex systems

By using **natural language syntax**, we make this power accessible to:
- Domain experts who shouldn't need to code
- Builders who think visually
- Teams that need shared understanding
- Organizations that value clarity

By separating **specification from implementation**, we ensure:
- Vendor neutrality
- Long-term stability
- Innovation without disruption
- Portability across platforms

**A22 makes agentic systems understandable, predictable, and accessible to everyone.**

The future of agents is functional. The future of agents is A22.

---

## Appendix A: Mathematical Foundation

For those interested in the formal foundations:

### A.1 Event Algebra

```
Event e ::= (type: Symbol, time: Timestamp, data: Map)

Context c ::= [e₀, e₁, ..., eₙ]  where time(eᵢ) ≤ time(eᵢ₊₁)

append :: Context → Event → Context
append(c, e) = c ++ [e]

State s is a projection: s = π(c) where π :: Context → State
```

### A.2 Pure Function Semantics

```
Agent a :: Context → Input → Event
  where ∀c, i: a(c, i) = a(c, i)  (referential transparency)

Workflow w :: Context → Input → [Event]
  where events are produced in causal order
```

### A.3 Temporal Logic

```
□P  — P holds for all events in context (always)
◇P  — P holds for some event in context (eventually)
P → Q — If P event occurs, Q event must follow
```

A22 programs can be verified using temporal logic.

---

## Appendix B: Event Types Reference

Common event types in A22 systems:

### B.1 Core Events
- `message.incoming` — User input
- `message.outgoing` — Agent output
- `agent.thinking` — Agent processing
- `agent.output` — Agent response
- `tool.request` — Tool invocation
- `tool.response` — Tool result
- `workflow.start` — Workflow begins
- `workflow.step` — Step executes
- `workflow.complete` — Workflow ends

### B.2 Control Events
- `hil.request` — Human input needed
- `hil.response` — Human provided input
- `policy.checked` — Policy validated
- `policy.violation` — Policy failed
- `schedule.tick` — Scheduled trigger

### B.3 State Events
- `preference.updated` — User preference changed
- `context.saved` — Context persisted
- `session.started` — New session
- `session.ended` — Session closed

All extensible. Implementations may add custom event types.

---

**© 2024 A22 Foundation**
**License: Apache 2.0**
