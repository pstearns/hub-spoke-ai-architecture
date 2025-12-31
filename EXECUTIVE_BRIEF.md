# Hub-Spoke: Governing Conversational State for Long-Horizon AI Work

### The Problem

In long-horizon work, linear chat mixes **decisions** with **exploration**. Over time, models will treat “what-if” branches as if they were approved decisions (authority drift), even when earlier decisions are still present in the transcript.

### Observed Failure Patterns

**Pattern A — Rejected options resurface as canonical**
- Decision: “We are not using microservices; too much overhead.”
- Later: the assistant proposes scaling “our microservices architecture” as if it was chosen.

**Pattern B — Exploration becomes precedent**
- Decision: “Backend is Python.”
- Exploration: “Is Node viable for one service?”
- Later: “Since we decided on Node, here are npm packages…” (silent overwrite)

**Pattern C — Constraints lose priority**
- Constraint: “Must run offline / no external APIs.”
- Later: assistant recommends cloud services as default, requiring repeated correction.

These failures occur even when the original decisions remain visible in the transcript, indicating a precedence failure rather than a recall failure.

### Why Memory Alone Fails

Retrieval solves recall, not authority.

* **Recall is not precedence**
  Remembering a decision does not ensure it is obeyed.

* **Similarity is not governance**
  Semantic relevance does not imply a rule should control future reasoning.

Without explicit authority, long-horizon conversations degrade regardless of context size.

---

### Validation Status & Scope

This document distinguishes between:

**Phase 1 (Core / MVP — Implementable Now)**
Concepts that have been observed in practice and are implementable with deterministic logic and human-in-the-loop review:
- **Canonical Hub vs exploratory Spoke state separation**
- **Explicit merge governance (UPSERT / REVISE / PRUNE)**
- **Hub precedence and Conflict Mode**
- **Manual merge review and versioned audit trail**

> “Phase 1 requires no ML classifiers; it is deterministic policy + UI.”

**Phase 2+ (Extensions — Optional)**
Mechanisms intended to reduce user burden or scale governance for larger systems:
- **Dependency graph (DAG) impact visualization**
- **Automated triage pipelines (retrieval, NLI, scoring)**
- **Advanced scope subsumption and conflict heuristics**
- **Adversarial benchmarking and stress-testing primitives**

Hub-Spoke remains correct and usable without Phase 2+ mechanisms; extensions are optimizations, not dependencies.

---

### The Hub-Spoke Model

Hub-Spoke introduces formal state governance for conversational work:

* **Hub**
  A governed, versioned source of truth for decisions, assumptions, and constraints.

* **Spokes**
  Isolated exploratory branches where reasoning can occur without polluting canonical state.

* **Merge**
  An explicit, user-governed checkpoint that promotes validated Spoke insights into the Hub.

This is not a metaphor. It is a state machine with lifecycle rules.

Hub-Spoke governs conversational state, not agent coordination or task execution.

Hub-Spoke ensures that exploratory reasoning can occur freely without ever silently overriding approved decisions, even across long-running, multi-session work.

### Scope and Non-Goals

**This is:**

* A governance layer for long-horizon reasoning
* A framework for preserving state integrity
* A human-in-the-loop authority model (Manual merge review)

*Note: The MVP implementation relies on manual merge review; automated analysis is advisory and optional.*

**This is not:**

* A memory system (it governs authority, not recall)
* An autonomous agent framework
* A conversational UI enhancement

### Where Hub-Spoke Applies

Hub-Spoke is intended for domains where silent drift has real cost:

* Software architecture and system design
* Research synthesis over extended timelines
* Strategic planning with evolving constraints
* Long-horizon project execution

For short-lived or low-consequence interactions, linear chat is sufficient.

### Further Reading

* Read the **Full Design Document (PDF)**
  `hub_spoke_architecture.pdf`

* Review the **Technical Specification (Markdown)**
  `hub_spoke_architecture.md`

* See implementation context in the **README**

Feedback and critique are welcome via GitHub issues.
