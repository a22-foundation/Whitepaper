A22: A Agent Native Language for Defining Agentic Systems

Whitepaper by the A22 Foundation

⸻

Abstract

Agentic systems are rapidly becoming part of everyday workflows, but defining agent behavior still feels like programming. A22 introduces a builder-friendly language specification—separate from any execution engine—that enables anyone to describe agent behavior clearly, deterministically, and without requiring technical knowledge.

A22 focuses on clarity, simplicity, fast feedback loops, and a visual mental model that mirrors how humans naturally describe tasks. It is vendor-neutral, execution-agnostic, and ensures the same A22 plan behaves the same way across implementations.

This whitepaper outlines the principles behind A22 and how the specification empowers non-technical agentic builders while offering extensibility for developers.

⸻

1. Introduction

Agentic systems are powerful, but the tools that define them are often too technical. Most require coding, orchestration logic, or familiarity with execution environments. This gap prevents many capable builders—people who understand the domain deeply—from shaping agent behavior.

A22 bridges this gap.

It provides a language specification designed to let anyone describe what an agent should be, know, and do using a structured, intuitive syntax. Instead of writing imperative instructions, builders define behavior through clear, declarative blocks that mirror how they naturally think.

A22 captures intent in a way that is precise enough for machines and accessible enough for non-technical builders.

⸻

2. Specification, Not Implementation

A core principle of A22 is strict separation between the language specification and any implementations.

2.1 The A22 Specification

The specification defines:
	•	The structure, grammar, and semantics of A22
	•	How agent behavior is described
	•	The meaning of each section (agent, tools, knowledge, plan, output, etc.)
	•	Determinism guidelines
	•	Validation rules

The specification does not prescribe:
	•	How an agent is executed
	•	What infrastructure is used
	•	Which model or runtime is chosen
	•	How tools are implemented internally

The language describes intent, not execution.

2.2 Implementations (Independent)

Implementations may take many forms:
	•	Cloud runtimes
	•	Edge device runtimes
	•	Local desktop interpreters
	•	Embedded engines
	•	Hybrid evaluators

Different implementations may vary in performance or features, but all must adhere to the specification’s semantics.

This guarantees:
	•	Same A22 plan → same behavior
	•	Execution-layer freedom
	•	No vendor lock-in
	•	Long-term stability for builders

Implementations innovate; the spec remains stable.

⸻

3. Purpose and Philosophy

A22 is built around the idea that defining agent behavior should feel intuitive and predictable—similar to expressing a structured thought.

Key philosophical pillars:

3.1 Builder-Friendly First

A22 is designed for non-technical agentic builders, not programmers.
The language reads like an organized outline of what the agent should do—not a script. It minimizes cognitive load and helps users visualize the full agent on a single page.

3.2 Configuration Over Code

The language leans toward a configuration-style structure.
This doesn’t reject code—it simply doesn’t require it. When code is needed, developers can extend or plug in modules without exposing complexity to non-technical users.

3.3 Deterministic Behavior

Given the same plan:
	•	inputs
	•	steps
	•	outputs
	•	checks

must behave consistently across implementations.

This builds trust and reliability for builders who don’t want to reason about execution internals.

3.4 Vendor-Neutral and Execution-Agnostic

A22 intentionally avoids coupling to:
	•	Cloud vendors
	•	Tool providers
	•	Model types
	•	Execution frameworks

The language expresses intent. Execution happens independently.

3.5 Minimal, Clear, and Evolving Only When Needed

A22 grows only when real-world builder needs emerge.
No feature exists because it’s technically interesting—only because it solves user pain without breaking simplicity.

⸻

4. Language Design Principles

The A22 language is shaped by a few guiding principles:

4.1 Intuitive Structure

Plans use blocks that reflect real human mental models:
	•	agent
	•	inputs
	•	knowledge
	•	tools
	•	checks / guards
	•	plan
	•	output

The structure mirrors natural explanation, enabling builders to visualize the agent.

4.2 Natural Flow of Thought

The language is declarative but avoids being rigid.
It should feel like describing how an agent behaves on a whiteboard, made precise enough for machines.

4.3 Simplicity for Non-Technical Users

A22 hides complexity until it’s needed.
Advanced logic is wrapped in extensible patterns familiar to developers, while non-technical builders continue working with intuitive blocks.

4.4 Fast Feedback Loops

A22 plans are small and easy to modify.
One-line changes should produce quick observable effects in any implementation.

4.5 Visual Mental Model

A22 plans map naturally to diagrams, trees, and flows.
This clarity helps builders iterate confidently and understand the system at a glance.

⸻

5. User Experience & Builder Focus

A22 is designed for people who know what they want an agent to achieve—but not necessarily how to program it.

It gives them:
	•	A structured, easy-to-read format
	•	Safe editing without fear of breaking code
	•	Predictability
	•	A one-page mental model

Developers also benefit:
	•	Extensibility points
	•	Custom tools
	•	Logic modules
	•	Hooks into advanced runtimes

But these capabilities never clutter the core builder experience.

⸻

6. Why A22 Matters Now

The agent ecosystem is expanding quickly.
Builders want:
	•	Predictability
	•	Portability
	•	Fast iteration
	•	Simplicity
	•	No vendor lock-in

Today’s agent tools lean heavily toward:
	•	imperative code
	•	complex orchestration
	•	vendor-specific systems
	•	tightly-coupled workflows

A22 provides a cleaner alternative:

a single, language-level specification for defining agent behavior—independent of implementation, approachable for all.

⸻

7. Governance & Community

A22 is guided by transparent, neutral governance focused on builder needs.

7.1 The Foundation as Steward

The A22 Foundation is a neutral steward—not a vendor.

It:
	•	Maintains clarity and minimalism
	•	Safeguards the specification
	•	Ensures predictability
	•	Does not control or mandate implementations
	•	Encourages open innovation at the execution layer

The Foundation guards the language, not the infrastructure around it.

7.2 Community-Driven Evolution

A22 evolves through the A22IP process (A22 Improvement Proposals).

Accepted changes must:
	•	simplify the language
	•	maintain backward compatibility where possible
	•	serve real builder needs
	•	keep the specification minimal and consistent
	•	strengthen the mental model

The community’s experience—especially from non-technical agentic builders—is central to decision making.

7.3 Versioning

A22 uses semantic versioning for the specification:
	•	0.x — Experimental
Exploration, prototyping, refinement.
	•	1.0 — Stable Core
The foundational, long-lived version.
	•	1.x — Incremental Improvements
Backward-compatible updates via A22IPs.

Versioning signals stability and ensures builders can rely on consistent behavior.

⸻

8. Roadmap

The roadmap guides the evolution of the specification, not implementations.

Phase 1 — Core Specification (Current)

Minimal, stable language elements: agents, tools, inputs, knowledge, plan, output, determinism rules.

Phase 2 — Early Builder Experience

Patterns, examples, diagrams, and documentation to help non-technical builders adopt A22.

Phase 3 — Extensibility Layer

Specification-level extension points for developers without affecting beginners.

Phase 4 — Ecosystem Alignment

Guidelines and conformance tools for independent implementations.

Phase 5 — Feedback & Refinement

Iterative, community-driven simplification based on real-world usage.

Phase 6 — Advanced UX for Builders

More powerful patterns that remain readable and intuitive.

Phase 7 — Long-Term Responsibility

Ensure A22 remains stable, neutral, simple, and deeply builder-centric.

⸻

9. Conclusion

A22 introduces a clear, structured, deterministic way to describe agent behavior—accessible to non-technical builders while flexible enough for developers.

By separating the specification from implementation, A22 guarantees vendor neutrality and long-term consistency.
By prioritizing builder experience, it makes agent creation intuitive, visual, and predictable.

A22 represents a step toward making agentic systems understandable and editable by everyone.
