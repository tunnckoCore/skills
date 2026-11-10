---
name: full-docs
description: Keep a repo's knowledge base current: the docs, the glossary, and the ADRs. Use when you write or update docs, when a change alters behavior or architecture, and before you file a PR.
---

# Full docs

The knowledge base tells agents and people what the project does and why, without reading every file. It is a snapshot of the current state. It is not a journal, a changelog, or a plan.

## Where knowledge lives

- **Terms:** `GLOSSARY.md`, or `GLOSSARY-MAP.md` plus one `GLOSSARY.md` per context. Use the `domain-modeling` skill and its format.
- **Decisions:** ADRs in `docs/adr/`. Use the `domain-modeling` skill and its format. Write one only when the decision is hard to reverse, surprising without context, and the result of a real trade-off.
- **Architecture, behavior, and test specs:**
  - If the repo has a `lat.md/` folder, write them there in its format: sections linked with `[[wiki links]]`, links into the code, and `@lat:` comments in the code that link back. Read the repo's lat instructions first. Do not edit files that lat generated.
  - Otherwise, write them as plain markdown in `docs/`, with one topic per file and an index that links each file with one line about it.

Create files only when you have something to write.

## First docs for a repo

When a repo has no docs yet, use the `codebase-docs` skill for what to cover: overview, structure, architecture, data flow, and config when it matters. Do not use its uppercase file names, "Last updated" dates, or changes logs. Write the topics in the layout above.

## Write docs

- State what the code does and why. Do not copy code.
- Name real paths, functions, and types, so a moved or renamed file makes the doc clearly wrong.
- Keep implementation details out of `GLOSSARY.md`.
- Do not repeat the README. Link to it.
- Use the `unslop` skill and ASD-STE100 Simplified Technical English.

## Keep docs current

A change that alters behavior, architecture, a term, or a decision updates the knowledge base in the same PR.

- Update only the affected sections. Do not rewrite the rest.
- Remove or fix every reference to a path or symbol that the change moved or deleted.
- Remove text that describes behavior that no longer exists. Do not mark it as old.
- If the change only refactors, and nothing a reader would look up changed, the docs stay as they are.
