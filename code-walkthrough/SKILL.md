---
name: code-walkthrough
description: Gives interactive, drill-down walkthroughs of how a project, system, or feature works, starting with a high-level orientation the user can steer deeper, down to line-by-line detail. Works on any kind of project (code in any language or engine, data pipelines, infrastructure, docs, design assets, spreadsheets). Use when the user asks for an explanation, tour, or walkthrough of how something works ("how does X work here?", "walk me through Y", "dig into Z", "explain this system").
---

# Code Walkthrough

The user wants to *understand* how something works (for example "how does request routing work here?", "how does this pipeline produce its report?", "how are the totals in this workbook calculated?"). You explain it top-down, grounded in the real project, then let them steer deeper with short commands like `dig into X`. Repeat until they reach line-by-line detail if they want it.

It works on any kind of project: software in any language or engine, games, data/ML pipelines, infrastructure, docs and writing, design and art assets, spreadsheets, research, hardware. Never assume a language, framework, or domain; learn it from the project first and use the project's own vocabulary. Below, "unit" means whatever the project is made of: module, class, document, sheet, asset, scene, pipeline stage, and so on.

Quoting and explaining what already exists in the project is the point. Do not write anything new unless asked.

---

## Ground everything in the real project

Before answering, look. Read the top-level layout, README/docs, manifest or build files, and a few representative files to learn what kind of project this is, how it is organized, and what conventions it follows. Then search and read the specific files for the topic (Grep/Glob/Read; for broad sweeps use a read-only sub-agent so file dumps stay out of the main context). Do this again at every deeper level; never use the previous level's summary as a substitute for reading.

- Never explain from general knowledge of a language, engine, or pattern when the project can answer the question.
- Cite every claim as a clickable `path:line` link. For things without lines (binary assets, spreadsheets, diagrams, slides), cite the path plus a locator: sheet and cell, object or node name, heading, page.
- If you could not find something, say so rather than guessing. Mark anything inferred rather than read as "(inferred)".
- If there is no project to read (empty directory, or a general topic), say so, then either ask what to look at or proceed from general knowledge, labeled as such.

## Navigation commands

The user steers with short commands. Accept natural variants ("go deeper", "zoom in", a menu number).

| Command | Effect |
|---|---|
| `dig into <X>` or a menu number | Make X the new focus and go one level deeper, scoped to X only |
| `back` | Up one level |
| `top` | Restart at Level 0 |
| `map` | Show the tree explored so far |

Free-form questions mid-walkthrough ("why is it done that way?") are welcome: answer them in the context of the current focus, then return to the menu. If the user jumps levels, go straight there with a one-line context recap first.

## Shape of every response

- **Bounded.** Level 0 is roughly one screen plus diagrams. Resist completeness; the menu is how they get the rest.
- **Breadcrumb** on everything below Level 0, so they know where they are: `Request handling > Auth > Token refresh`.
- **End with a menu** of 3-7 numbered next steps, each with a one-line teaser, plus `back` / `top` / `map` where relevant.
- **Track the explored tree** so `map` and `back` work.
- If a sub-area turns out to be huge (many files, a giant sheet), say how big it is and suggest a narrower entry point rather than skimming it all.

## Diagrams

Output renders as markdown, often in a terminal, so Mermaid usually will not render. Default to **ASCII/box diagrams in fenced code blocks**, under ~80 columns, unless you know the user's environment renders something richer. Useful kinds:

- **Architecture/component**: boxes and arrows for the pieces and ownership.
- **Sequence/timeline**: a column per actor with time flowing down. Use for anything with several parties over time: network exchanges, pipeline stages, workflows, handoffs.
- **Flowchart**: branching logic of one function, procedure, or process.
- **State machine**: lifecycles.
- **Data layout**: structs, packets, buffers, schemas, sheet or table layouts.
- **Tables**: comparisons and mappings.

Use a diagram when it explains faster than prose, not decoratively. If the user wants a rendered/shareable version (Mermaid, SVG, or an HTML page), write it to a file inside the working directory and link it.

---

## Level 0: Orientation (first answer to a topic)

1. **One-paragraph summary**: what the system does and the core idea in plain language.
2. **Diagram(s)**: the major pieces and how data/control flows between them. For multi-party or time-based systems also include a sequence/timeline diagram.
3. **Key components table**: name, role, where it lives (`path:line`).
4. **The tricky part**: the one or two non-obvious ideas that make it work (an ownership or ordering rule, a consistency trade-off, a convention everything depends on).
5. **Dig-deeper menu**: 3-7 numbered sub-areas the user could drill into, each with a one-line teaser. End by inviting `dig into <X>`.

Do not dump every detail at Level 0.

## Drilling down: `dig into X`

Treat X as the new focus and run the same process one level lower, scoped to X only:

- Start with a breadcrumb.
- Show a more detailed diagram of just X, the units and files involved, and how X connects back to its parent.
- End with a new dig-deeper menu for X's children, plus `back`, `top`, `map`.

Depth ladder (go only as deep as asked):

| Level | Scope | Shows |
|---|---|---|
| 0 | Whole project or system | Architecture diagram, components, menu |
| 1 | Subsystem or area | Responsibilities, data flow, key types or documents |
| 2 | Unit (class, module, document, sheet, asset) | Contents, lifecycle, who uses whom |
| 3 | Element (function, section, formula, node, config block) | Control flow, inputs/outputs, edge cases |
| 4 | Line by line | Walk each line or block in order, explaining what it does and why |

At Level 4, quote small chunks (a few lines, rows, or steps at a time) in order, each followed by an explanation: purpose, what the names mean, why that order, what would break if changed, gotchas. Pause between chunks on long passages and ask whether to continue, skip ahead, or dig into something it references. Link each chunk with `path:line`.

## Keeping the map

```
Request handling
├─ Routing                  (explored)
├─ Authentication           <- you are here
│  ├─ Token validation
│  └─ Token refresh
└─ Response rendering
```

Offer to check understanding sparingly (one optional question), never as a gate.
