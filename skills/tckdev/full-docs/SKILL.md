---
name: full-docs
description: >
  Write and keep current a repo's knowledge base for agents and people: the docs,
  the glossary, and the ADRs. Use when you document a codebase, update docs after
  a change, or when a change alters behavior, architecture, a term, or a decision.
  Not for README files, code comments, API references, or user-facing guides.
---

# Full docs

The knowledge base tells agents and people what the project does and why, without reading every file. It is a map of the current state. It is not a journal, a changelog, or a plan.

## Where knowledge lives

- **Terms:** `GLOSSARY.md`, or `GLOSSARY-MAP.md` plus one `GLOSSARY.md` per context. Use the `domain-modeling` skill and its format.
- **Decisions:** ADRs in `docs/adr/`. Use the `domain-modeling` skill and its format. Write one only when the decision is hard to reverse, surprising without context, and the result of a real trade-off.
- **Architecture, behavior, and test specs:**
  - If the repo has a `lat.md/` folder, write them there in its format: sections linked with `[[wiki links]]`, links into the code, and `@lat:` comments in the code that link back. Read the repo's lat instructions first. Do not edit files that lat generated.
  - Otherwise, write them as plain markdown in `docs/`, one topic per file, with lowercase file names.

Create files only when you have something to write.

## First docs for a repo

Read before you write:

1. Read the root: the README, the package or build manifests, the config files, and any existing docs. If docs exist, you update them. You do not start again.
2. Map the directories to what each one is responsible for.
3. Find the stack, the entry points (main files, routes, CLI commands, exported modules), and the architecture style.

Then cover these topics, in the layout above:

- **Always:**
  - `overview.md`: what the project is for, the stack, the architecture style, and the key concepts. Agents read it first.
  - `structure.md`: the directory tree, with what lives where and why.
  - `architecture.md`: how the parts connect, the data flow, and the key abstractions.
- **Only when the project has them:** `data-model.md`, `api.md`, `configuration.md`, `patterns.md`, `deployment.md`, `development.md`.
- **Index:** `docs/index.md` links every doc with one line about it, so an agent can pick the right file. Write it last.

A small project needs fewer files. Do not write a file to fill a slot.

## Write docs

- State what the code does and why. Do not copy code.
- Name real paths, functions, and types, so a moved or renamed file makes the doc clearly wrong.
- State relationships explicitly: "Module A calls `process()` in Module B when..."
- Show data flow in one direction: "Request → Router → Service → Repository → Database".
- State assumptions and constraints the code does not show.
- Start each file with two or three sentences about what it covers.
- Document public interfaces and key abstractions, not every function. Skip generated files and dependencies.
- Keep implementation details out of `GLOSSARY.md`.
- Do not link from docs to the README.
- No "Last updated" dates or changes logs. Git has the history.
- Use the `unslop` skill and ASD-STE100 Simplified Technical English.

## Keep docs current

A change that alters behavior, architecture, a term, or a decision updates the knowledge base in the same PR.

- Update only the affected sections. Do not rewrite the rest.
- Remove or fix every reference to a path or symbol that the change moved or deleted.
- Remove text that describes behavior that no longer exists. Do not mark it as old.
- ADRs are the exception. Never delete or rewrite an ADR whose decision changed. Mark it `superseded by ADR-NNNN`, and link the new ADR back to it.
- If the change only refactors, and nothing a reader would look up changed, the docs stay as they are.

## Check before you finish

- Every path and symbol named in the docs exists.
- The tree in `structure.md` matches the repo.
- The index links every doc, and every cross-link resolves.
