# Documentation requirements

Alongside the implementation, add documentation in two places:

1. **In code**: add doccomments to relevant functions introduced or changed by the implementation.
2. **In markdown**: create one concise architecture document for the implemented feature.

This is not generic documentation work. Focus on **intent, architecture, boundaries, and future maintainability**.

---

### 1) Doccomments in code

Add doccomments only where they materially improve understanding, especially for:

- exported functions
- composables, services, utilities, handlers, factories, adapters
- non-trivial internal helpers

Do **not** comment trivial or self-explanatory code.

Doccomments must explain:
- why the function exists
- what responsibility it owns
- how it fits into the feature flow
- key assumptions, invariants, side effects, or failure behavior where relevant

Avoid:
- line-by-line narration
- restating the function name
- filler with no architectural value

Keep comments concise and aligned with the project’s existing style.

---

### 2) Architecture markdown file

Create **one** markdown file that is optimized for **AI agent ingestion** but still easy for humans to scan.

The document must be short, dense, and implementation-grounded. It should help a reader quickly understand:

- what the feature is for
- why it exists
- which files matter
- how the runtime/control flow works
- what constraints and extension points exist

Use this structure unless the code strongly suggests a better variant:

# `<Feature name>`

## Purpose
What it does and why it exists.

## When to use / not use
Use cases, boundaries, non-goals.

## Architectural overview
Main parts and responsibilities.

## Code map
Important files/modules and their role.

## Execution flow
Actual control/data flow through the implementation.

## Key design decisions
Important choices and tradeoffs.

## Constraints and invariants
Assumptions and guarantees future changes must preserve.

## Extension guidance
Where new behavior should be added, and where not.

## Risks / pitfalls
Coupling points, failure modes, easy mistakes.

## Summary
Short closing mental model.

---

### Quality bar

The documentation must be:
- concise
- high-signal
- architecture-first
- grounded in the actual code

It must not be:
- a full project README
- a changelog
- a tutorial
- line-by-line code paraphrasing
- fluffy abstraction nonsense

Do not invent architecture that is not present in the implementation. If something is unclear, state that briefly instead of guessing.

---

### Acceptance criteria

Complete the task only if:

- relevant functions have useful intent-focused doccomments
- doccomments explain **why / role / constraints**, not just **what**
- one concise architecture markdown file was added
- the markdown reflects the real code structure and flow
- unrelated files were not touched without good reason
