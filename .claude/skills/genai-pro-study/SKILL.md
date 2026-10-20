---
name: genai-pro-study
description: Drive AWS Certified Generative AI Developer – Professional (AIP-C01) study in this repo. Use when the user says "next topic", "continue genai", "cover <topic>", pastes an AIP-C01 practice question, or asks for coverage status. Tracks progress in 17-AWS-GenAI-Developer-Professional/TRACKER.md and writes in-depth notes.
---

# AIP-C01 Study Workflow

Notes live in `17-AWS-GenAI-Developer-Professional/`. The checklist of everything the exam guide requires is `TRACKER.md` there. Work happens on branch `genai-developer-professional`; never merge to `main` until the user says the learning is complete.

## Before anything

1. `git branch --show-current` — must be `genai-developer-professional`. If not, stop and tell the user.
2. Read `17-AWS-GenAI-Developer-Professional/TRACKER.md`.

## Modes

**Next topic** ("next", "continue"): take the first unticked task in domain order, unless the user names one. Prefer finishing a half-covered (`[~]`) task before opening a new one.

**Named topic** ("cover Bedrock Guardrails"): find the tracker skills it maps to (and the Cross-Cutting backlog entry), cover it, tick every skill it satisfies.

**Practice question**: answer it, explain why each wrong option is wrong, map it to tracker skill IDs, grep the notes for the missing fact, and add only that gap (one bullet or short section). Tick nothing unless the skill is now fully covered.

**Status**: summarize ticked/total per domain, weighted by domain %, and name the next recommended task. No file changes.

## Covering a topic in depth

1. `grep -ri` the notes folder and the SAA folders for existing coverage; link to it (relative link) rather than duplicating. Relevant SAA material: `06-AWS-Compute-Services` (Lambda, API Gateway, Step Functions), `08-AWS-Database-Services`, `10-AWS-Monitoring-and-Management`, `13-Data-Analytics-Services` (OpenSearch), `14-Machine-Learning-Services`, `15-AWS-Security-Services`.
2. Research current AWS docs with WebSearch/WebFetch (docs.aws.amazon.com). GenAI services change fast (model IDs, quotas, features); do not rely on memory for limits, supported regions/models, or API names. Record exact numbers and API/parameter names.
3. Write or extend notes in the **service folder** that owns the topic (see the folder table in `TRACKER.md`). Same layout as the SAA folders: a service with several facets gets a folder with `README.md` (definition, Key Concepts, diagram, file index table) plus numbered sub-files (`01-Chunking.md`); a small topic is a single `.md` file. Put content where the service lives, not where the exam domain lists it; other services link to it with relative links. Delete a folder's `.gitkeep` once it has real content. Add a new numbered folder only when no existing one fits, and update the `TRACKER.md` folder table.
4. Depth checklist per topic: what it is and why it exists; how it works (ASCII flow); configuration options with defaults/limits; API names and key parameters; IAM/network/encryption angle; cost and quota gotchas; failure modes; "X vs Y" confusions with neighboring services; a short "When to choose" for scenario questions. "In depth" means the scenario-discriminating details a Professional-level question hinges on, not length.
5. Update `TRACKER.md`: flip skill boxes (`[x]` full, `[~]` partial), add a note-file pointer after the skill text as `→ [file](path)`, tick the parent task when all its skills are `[x]`, and tick Cross-Cutting backlog items.
6. End the reply with: what was covered, which skill IDs ticked, and the next suggested topic.

## Style (inherits repo CLAUDE.md)

- 1–2 sentence definition first; `**Key Concepts:**`; bullets as `- **Term**: explanation — real numbers/limits`; `---` between sections; tables for comparisons; ASCII diagrams for flows; `**X vs Y:**` block when commonly confused.

### Visual-first format

Notes are for fast revision and first-time understanding: show the idea, then give the facts. A topic is never a wall of bullets, and never only tables. Choose the visual by what the reader must grasp.

| To show | Visual | Notes |
|---|---|---|
| How a thing works end to end | Mermaid `flowchart LR/TD` | Label arrows with the data that moves |
| Who calls whom, in what order (APIs, agents, auth) | Mermaid `sequenceDiagram` | Include the API/operation names on arrows |
| Which option to choose | Mermaid decision flowchart (`{question?}` nodes) | Leaf = the pick; one per topic, at the end |
| Nesting / hierarchy / parent-child | ASCII tree or Mermaid `flowchart` with subgraphs | e.g. parent chunk ⊃ child chunks |
| Concrete behavior of a setting | Worked example with real numbers, before → after | e.g. 101 sentences, threshold 90 → 11 chunks |
| Options of one thing | Table, one row per option | Columns: how it works, limits, gotcha |
| A vs B | Table with identical attribute rows + "Pick when" row | Never prose |
| Parameters / API fields | Table: `Name \| Default \| Range \| Effect` | |
| Error / symptom | Table: `Symptom \| Likely cause \| Fix` | |
| Scenario → answer | Table: `Need \| Pick` | Final section of the topic |
| Small facts and limits | One-line bullets: `**Term**: fact — number` | ≤ 25 words |

**Page skeleton** (use what applies, in this order):
1. One-line definition, then a **mental model** line: an analogy or "X is to Y what…" (`> **Mental model:** …`).
2. **Diagram** of the main flow (Mermaid, ≤ ~12 nodes).
3. **Key Concepts** bullets.
4. Option/parameter **tables**.
5. **Worked example** with numbers.
6. **Callouts** for traps and tips (see below).
7. **Decision flowchart** and/or `Need | Pick` table.

**Callouts**: use GitHub alert blockquotes, sparingly (max ~3 per page), so traps stand out:
- `> [!WARNING]` for gotchas, limits that bite, irreversible settings
- `> [!TIP]` for the preferred choice or shortcut
- `> [!NOTE]` for context that changes interpretation

**Mermaid rules**: GitHub renders it natively; VS Code preview needs the "Markdown Preview Mermaid Support" extension. Keep node text ≤ 5 words, quote labels containing punctuation (`A["Embed (Titan)"]`), avoid `(` `)` unquoted in labels, and one idea per diagram. Fall back to an ASCII diagram for tiny 3–4 box flows. Do not use Mermaid where a table is the clearer form.

**Rules**:
- **Visual before text**: diagram or table first, explanatory bullets after; if a paragraph restates the diagram, delete it.
- **Table-first for shared attributes**: two or more items sharing attributes = a table. Cells ≤ ~12 words, fragments over sentences.
- **Front-load**: bold the term or number the reader scans for; decisions go last.
- **One idea per section**; `##` headings name what you look up (`Chunking Strategies`, `Throttling Errors`), not `Overview`/`Details`.
- **Gotchas are visible**: `Gotcha` column or `> [!WARNING]`; quotas and limits live in tables, not sentences.
- **Worked examples are numeric and small**; invent no AWS facts to make an example, use real documented limits or clearly generic numbers.
- Touching an existing note: convert the prose you touch to the right visual; leave untouched sections alone.
- No "key exam points" sections, no exam labels, no trailing summaries, no console walkthroughs, no IaC code unless the topic is IaC. Short SDK/API snippets are fine only when the exact request shape is what gets tested (e.g. Converse API, ApplyGuardrail).
- No duplicate content; no rewrites of unrelated parts.

## Git

- Edit files only; commit only when the user asks. Commit message style matches history: descriptive sentence ending in `[AIP-C01]`.
- Merge happens only on explicit user go-ahead. Before proposing it, confirm every `TRACKER.md` box is `[x]` and list any that are not. Merge with `git checkout main && git merge --no-ff genai-developer-professional`; do not push or delete the branch without being asked.
