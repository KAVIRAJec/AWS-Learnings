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

1. **Duplicate check first** (see *No-duplication rules* below): `grep -ri` the notes folder and the SAA folders for each term/number you are about to write; link to the existing home instead of restating it. Relevant SAA material: `06-AWS-Compute-Services` (Lambda, API Gateway, Step Functions), `08-AWS-Database-Services`, `10-AWS-Monitoring-and-Management`, `13-Data-Analytics-Services` (OpenSearch), `14-Machine-Learning-Services`, `15-AWS-Security-Services`.
2. Research current AWS docs with WebSearch/WebFetch (docs.aws.amazon.com). GenAI services change fast (model IDs, quotas, features); do not rely on memory for limits, supported regions/models, or API names. Record exact numbers and API/parameter names.
3. Write or extend notes in the **service folder** that owns the topic (see the folder table in `TRACKER.md`). Same layout as the SAA folders: a service with several facets gets a folder with `README.md` (definition, Key Concepts, diagram, file index table) plus numbered sub-files (`01-Chunking.md`); a small topic is a single `.md` file. Put content where the service lives, not where the exam domain lists it; other services link to it with relative links. Delete a folder's `.gitkeep` once it has real content. Add a new numbered folder only when no existing one fits, and update the `TRACKER.md` folder table.
4. Decide what this topic needs, then cover it in depth: why it exists, how it works, the settings that change behavior (with defaults/limits), API names and key parameters where the request shape matters, IAM/network/encryption/cost/quota angles where relevant, how it fails, and how it differs from neighboring services. Not every topic needs every angle. "In depth" means the details a Professional-level scenario question hinges on, not length. Skip anything already owned by another file (link instead).
5. Update `TRACKER.md`: flip skill boxes (`[x]` full, `[~]` partial), add a note-file pointer after the skill text as `→ [file](path)`, tick the parent task when all its skills are `[x]`, and tick Cross-Cutting backlog items.
6. End the reply with: what was covered, which skill IDs ticked, and the next suggested topic.

## No-duplication rules

Every fact has exactly **one home**. Other files link to it with a relative link; they never restate it.

| Rule | Detail |
|---|---|
| Home = the file about that service/feature | e.g. embedding dimensions live in the vector-store options note; chunk strategies in `01-Chunking.md` |
| Link, don't copy | One short clause plus a link (`how it works: [Hybrid Search](…)`), not a second table |
| READMEs are indexes | Definition, one diagram, file table, pending list. **No** decision tables, parameter tables, or content already in a sub-file. Exception: one cross-file comparison that belongs to no single sub-file |
| One diagram per idea | The pipeline/flow diagram lives in one file; others link. No diagram that only restates a table in the same file (or vice versa) |
| One decision table per file | `Need → Pick` only when its rows are not already visible in that file's tables or flowchart; delete rows that repeat a table above |
| One troubleshooting table per topic | Symptom rows already implied by another file's table are linked, not repeated |
| Same fact in two services | Put it in the service that owns the setting; the other gets a link |
| Definitions once | Full definitions and tables live in one note. Elsewhere give a one-phrase plain recap plus a link, so the reader is not forced to leave the page to follow it |
| Before saving | Re-read the new file against its neighbors: any table, number, or sentence that also exists elsewhere is cut or turned into a link |
| Extending a note | Add only the missing fact; if it already exists elsewhere, link and stop |

## Writing for understanding

Goal: someone meeting the topic for the first time can follow the note, and someone revising can find a fact in seconds. There is no fixed template. For every topic, decide what the reader needs to *understand* and what they need to *look up*, then choose the form that fits each piece.

### Think before writing

1. **What is this topic really?** A concept to grasp (why it exists, how it behaves), a flow, a set of options to choose between, a list of limits and parameters, a failure to diagnose, or a mix? Notes on different topics should look different.
2. **Who is reading?** Assume they know AWS exists but not this feature's vocabulary. Define each term in a plain sentence the first time it appears, and say *why it matters*, not only what it is.
3. **What is the one thing they must walk away with?** Lead with it. Cut what doesn't serve it.

### Pick the form from the content

Use judgement. These are tendencies, not quotas:

- **Prose or short paragraphs**: explaining an idea, a cause and effect, a trade-off, why something exists. This is where understanding happens; do not compress it into fragments.
- **Diagram**: a flow, an order of calls, fan-out, nesting, a decision. Only when it shows something the words don't.
- **Worked example with numbers**: whenever a setting or formula is hard to picture. Small, real or clearly generic numbers.
- **Table**: reference material the reader will scan or compare, such as parameters with defaults and ranges, limits and quotas, supported/unsupported matrices, metric name → what it indicates, option A vs B across the same attributes. Use tables freely when the content is of this shape, and nowhere else.
- **Bullets**: a handful of related facts or steps that don't compare across attributes.
- **Callout** (`> [!WARNING]`, `> [!TIP]`, `> [!NOTE]`): a trap, a preferred choice, or context that changes interpretation. Sparingly.
- **Numbered steps**: a real sequence.

A single note usually mixes several of these. A concept-heavy topic may be mostly prose and one diagram; a limits-heavy topic may be mostly tables. If a table's cells are full sentences, it should be prose. If a paragraph is a list of numbers, it should be a table. If a diagram only repeats a list, drop it.

### Quality checks before saving

- Could someone new follow it top to bottom? Is every acronym and term defined where first used?
- Is each piece in the form that fits it, and is anything forced into a form it doesn't suit?
- Does any fact, table, or diagram also exist in another file? If so, link instead (no-duplication rules above).
- Are the numbers real and documented, or clearly labeled generic? No invented AWS facts.

### Mechanics

- `##` headings name what the reader is looking up (`Hot Shards`), not `Overview`.
- Mermaid renders on GitHub; VS Code preview needs the "Markdown Preview Mermaid Support" extension. Keep node text short, quote labels with punctuation, one idea per diagram.
- No "key exam points" sections, no exam labels, no trailing summaries, no console walkthroughs, no IaC code unless the topic is IaC. Short SDK/API snippets only when the exact request shape matters.
- Touching an existing note: rewrite what you touch to fit these ideas; leave the rest.

## Git

- Edit files only; commit only when the user asks. Commit message style matches history: descriptive sentence ending in `[AIP-C01]`.
- Merge happens only on explicit user go-ahead. Before proposing it, confirm every `TRACKER.md` box is `[x]` and list any that are not. Merge with `git checkout main && git merge --no-ff genai-developer-professional`; do not push or delete the branch without being asked.
