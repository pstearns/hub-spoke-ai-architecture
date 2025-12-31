# Hub-Spoke: A Design Framework for Long-Horizon AI Conversations

**A proposal for managing conversational state through governed branching and merge semantics.**

**Author:** Peter Stearns  
**Status:** Design Proposal (open for feedback)  

📘 **Executive Brief (6-8 minute read):**  
A concise overview of the problem, core idea, and when Hub-Spoke applies.  
→ [`EXECUTIVE_BRIEF.md`](./EXECUTIVE_BRIEF.md)

> This framework exists because increasing context length does not solve authority drift in long-running AI workflows.

**📄 Full Design Document:** [`hub_spoke_architecture.pdf`](./hub_spoke_architecture.pdf)

---

## Validation Status & Scope

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

## The Problem: Authority Drift

In long-horizon work, linear chat mixes **decisions** with **exploration**. Over time, models will treat “what-if” branches as if they were approved decisions (authority drift), even when earlier decisions are still present in the transcript.

## Observed Failure Patterns (from sustained project work)

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

**The core failure is not recall. It is authority.** The system lacks a structural mechanism to weight "decisions" above "exploration."

---

## The Hub-Spoke Model

**Hub-Spoke** separates conversational state into three primitives:

| Primitive | Purpose |
|-----------|---------|
| **Hub** | Canonical state: decisions, assumptions, constraints, open questions |
| **Spoke** | Isolated exploration branches; inherit Hub context but cannot mutate it |
| **Merge** | Explicit, user-approved operations that update the Hub |

This is not a chat interface. It's a **state governance layer** for long-horizon reasoning. It governs conversational state, not agent coordination or autonomous task execution.

Hub-Spoke allows exploration to proceed freely without ever silently overriding approved decisions, even across long-running, multi-session work.

---

## Hub Data Model

Each Hub entry contains:

| Field | Type | Purpose |
|-------|------|---------|
| `id` | UUID | Unique identifier |
| `atom` | SemanticAtom | The core (Predicate, Value, Scope, Evidence, Rationale) |
| `type` | Decision / Assumption / Constraint / OpenQuestion | High-level state classification |
| `status` | Active / Deprecated / Pending | Entry lifecycle state |
| `confidence_band` | High / Medium / Low | Triage signal for merge governance |
| `source_spoke` | String | Originating branch |
| `last_reviewed` | Timestamp | User approval audit trail |
| `supersedes` | UUID[] | Replacement graph (versioning) |
| `links_to` | UUID[] | Dependency graph (DAG of related decisions) |

The Hub intentionally stores only canonical state, never raw transcripts or chain-of-thought.

This schema enables audit trails, reversibility, and human-governed authority using a DAG-based dependency model.

---

## Implementation Roadmap

**Phase 1: State Separation (Core Primitives)**
- Implementation of Hub (Postgres) and Spoke (Transient Chat) storage.
- Manual Merge UI for state transitions.
- Immutable append-only versioning for Hub entries.

**Phase 2: Automated Integration Triage**
- Semantic decomposition of Spoke output into discrete atoms.
- Embedding-based similarity matching (pgvector) against the Hub.
- Automated proposal of UPSERT/REVISE/PRUNE operations.

Note: The MVP implementation relies on manual merge review; automated analysis is advisory and optional.

**Phase 3: Cross-Spoke Synchronization**
- Concurrent Spoke tracking.
- Triangulation to identify global convergence/divergence.
- Conflict detection dashboard for multi-branch exploration.

**Suggested Stack:**
- **Frontend**: React with markdown editor
- **Backend**: FastAPI (Python) or Node.js/Express
- **Storage**: PostgreSQL (Hub entries) + pgvector (embeddings)
- **Embeddings**: sentence-transformers or OpenAI API
- **Audit**: Immutable append-only ledger

This phased approach ships governance in Phase 1, then adds accelerators based on feedback.

---

## Scope and Intent

This design is **not** intended for all AI interactions. It is scoped for:

- **Long-horizon reasoning** on sustained projects
- **Architecture and design work** requiring coherent decisions
- **Research synthesis** with evolving knowledge
- **Strategic planning** that must revisit assumptions
- Any **sustained, high-complexity intellectual work**

For short-lived or low-stakes interactions, linear chat is preferable and simpler.

---

## In This Repository

- 📄 **`hub_spoke_architecture.pdf`**: Complete design document with failure modes, architecture, schemas, roadmap
- 📝 **`hub_spoke_architecture.md`**: Markdown source (version-controlled)
- 🖼 **`images/`**: Architecture diagrams (topology, vector space diffs, governance flow)
- 📋 **`README.md`**: This file

**This is a design proposal, not production code.** The roadmap describes how to build it incrementally.

---

## Feedback & Contact

This is a **design proposal** open for feedback and collaboration.

If you're:
- Building AI memory or governance systems
- Designing conversational UX for long-horizon work
- Researching conversational state management
- Interested in implementing this

Please reach out or open an issue:

**GitHub:** https://github.com/pstearns/hub-spoke-ai-architecture

---

## Acknowledgments

**Design & Architecture:** Peter Stearns

**Drafting & Iteration Assistance:**
Claude (Anthropic), ChatGPT (OpenAI), Gemini (Google) - used as analytical and editorial tools under direct human control.

This represents a human-authored synthesis of established techniques (vector embeddings, versioned state, governed mutation) into a coherent framework for AI interaction.

---

## License

Licensed under [Creative Commons Attribution 4.0 International](https://creativecommons.org/licenses/by/4.0/)

You are free to share, adapt, and build upon this work with appropriate attribution.

---

## Citation

If you reference this design in research, product, or documentation:

```text
Stearns, P. (2025).
Hub-Spoke: A Design Framework for Long-Horizon AI Conversations.
GitHub. https://github.com/pstearns/hub-spoke-ai-architecture
```
