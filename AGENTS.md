# Comic adaptation project

Turn user-provided prose into a navigable HTML adaptation workspace: Markdown originals → source HTML → entity manifest → screenplay HTML → entity prompt pages → later visual phases.

**Working mode:** Human-led, one unit of work per run. The **stan-lee** preset routes to the numbered skill for the step the user names. Steps 1.1 and 1.2 are project-wide with **continue** (one chapter scan, or one entity×chapter slice).

---

## Prerequisites

The user supplies organized Markdown source chapters before agent work begins:

- One file per chapter under `story/original/<chapter>.md` (kebab-case slug, e.g. `0-prologue.md`).
- Optional YAML frontmatter with `title` only.
- No agent ingest phase — do not rewrite or split manuscripts unless the user explicitly asks outside the pipeline.

---

## How to work (stan-lee)

1. The user states **step** and optionally **chapter** (e.g. Step 1, `0-prologue`; or Step 1.1 / 1.2 with **continue**).
2. Read `.pi/skills/<n>-<name>/SKILL.md` for that step and follow it.
3. Structural rules in this file always apply.
4. End with: what changed, open questions, and a **suggested** next step — do not start the next step without confirmation.
5. If a request spans steps, ask which step to do first.

---

## Early pipeline

| Step | Skill | Output |
|------|-------|--------|
| 1 | `1-convert-originals` | `story/html/<chapter>.html` |
| 1.1 | `1-1-entity-manifest` | `story/adapted/entities.html` (merge one chapter per run) |
| 1.2 | `1-2-entity-prompts` | `prompts/<entity>/<entity>-base.html` (one entity×chapter slice per run) |
| 2 | `2-adapt-screenplay` | `story/adapted/<chapter>-screenplay.html` |

Later visual phases are intentionally out of scope for this early pipeline. Step procedures, templates, and “done when” criteria live in each skill, not here.

---

## Repository layout

| Path | Purpose |
|------|---------|
| `story/original/` | User-provided Markdown chapters (prerequisite) |
| `story/html/` | Generated source HTML with stable paragraph anchors |
| `story/adapted/` | Project entity manifest, per-chapter screenplays |
| `story/adapted/entities.html` | Project-wide entity catalog (Step 1.1 / 1.2) |
| `prompts/<entity>/` | Entity prompt HTML pages and later reference assets |
| `.pi/mypi/agents/stan-lee.yml` | Stan-lee preset |
| `.pi/skills/<n>-<name>/` | Step skills |

---

## Link rules

- Artifacts use real HTML links, not wiki links.
- Use relative `href` values so files work in a browser from the local filesystem.
- Generated source paragraphs use stable IDs like `p-001`; provenance links should point to `../html/<chapter>.html#p-001` or equivalent relative paths.
- **Project manifest** (`story/adapted/entities.html`): `data-chapters-scanned` lists chapters merged in Step 1.1. Each entity row has **Chapters** (`ul.chapter-refs` with `data-chapter` per appearance).
- **Name cells:** plain text until Step 1.2 creates `prompts/<slug>/<slug>-base.html`, then link (entity opened—not “all chapters done”).
- **Chapter refs:** pending slices link to source HTML only; `data-slice-done="true"` links to `#evidence-<chapter>` on the prompt page plus source `p-xxx` in parentheses.
- Screenplay (per chapter) links to `entities.html` and to prompt pages when they exist.
- **Step 1.1 complete:** every `story/html/<chapter>.html` slug is in `data-chapters-scanned`.
- **Step 1.2 complete:** every `chapter-refs` item has `data-slice-done="true"` and a non-empty evidence section on the prompt page.
- **Retired:** `story/adapted/<chapter>-entities.html` — do not create.

---

## Core conventions

- Filenames: kebab-case slugs.
- User originals remain Markdown under `story/original/`; generated adaptation artifacts are HTML.
- HTML should be simple, valid, and template-driven. Avoid inline styling and JavaScript in artifacts.
- Use semantic classes for screenplay structure (`scene`, `scene-heading`, `action`, `character`, `dialogue`).
- Update this file when cross-cutting structure changes; update the relevant step skill when step behavior changes.

---

## HTML contracts (summary)

Detailed templates and examples are in the step skills.

| Artifact | Step skill |
|----------|-------------|
| `story/html/<chapter>.html` | `1-convert-originals` |
| `story/adapted/entities.html` | `1-1-entity-manifest` |
| `prompts/<entity>/<entity>-base.html` | `1-2-entity-prompts` |
| `story/adapted/<chapter>-screenplay.html` | `2-adapt-screenplay` |

---

## Project index

Run from the repository root:

```bash
./read
```

Regenerates `index.html` from files on disk (chapters, pipeline step status, links) and opens it in a browser. Use `./read --no-open` to generate without launching.

---

## Preset

**stan-lee** (`.pi/mypi/agents/stan-lee.yml`): comic adaptation assistant. Project context includes this file via `includeContextFiles`. The human chooses the step; the agent reads the matching skill under `.pi/skills/`.
