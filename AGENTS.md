# Comic adaptation project

Turn user-provided prose into a navigable HTML adaptation workspace: Markdown originals → source HTML → entity manifest → screenplay HTML → entity prompt pages → later visual phases.

**Working mode:** One unit of work per run. The user may name a step explicitly, or say **`continue`** alone—the agent reads `story/adapted/entities.html` and does exactly **one** next unit (one chapter scan, one entity×chapter slice, or one screenplay chapter).

---

## Prerequisites

The user supplies organized Markdown source chapters before agent work begins:

- One file per chapter under `story/original/<chapter>.md` (kebab-case slug, e.g. `0-prologue.md`).
- Optional YAML frontmatter with `title` only.
- No agent ingest phase — do not rewrite or split manuscripts unless the user explicitly asks outside the pipeline.

---

## How to work (stan-lee)

1. If the user says **`continue`** (only), follow **Bare continue** below—do not ask which step.
2. Otherwise the user may state **step** and optionally **chapter** (e.g. Step 1, `0-prologue`).
3. Read `.pi/skills/<n>-<name>/SKILL.md` for that step and follow it.
4. Structural rules in this file always apply.
5. **Persist:** use `write` or `edit` on every output file you change; re-read the file to verify before claiming done. Never report a merge or slice as complete from chat alone.
6. End with: what changed (paths only if written), open questions, and **suggested next step** (usually “say **continue**”). Do not start a second unit in the same run.
7. If a request spans steps, ask which step to do first.

---

## Bare continue

Fresh context starts here—no chat history required.

1. **Open** `story/adapted/entities.html` when it exists; otherwise infer Step 1 or Step 1.1 from `story/html/` and `story/original/`.
2. **Read first** (top of manifest, in order):
   - `#pipeline-next-1-1` and `data-chapters-scanned` on `<main>`
   - `#pipeline-next-1-2` and `data-next-step`, `data-next-entity`, `data-next-chapter` on `<main>`
3. **Pick exactly one skill** (first match wins):

| Condition | Skill | One unit |
|-----------|--------|----------|
| Some `story/html/*.html` missing and user has not finished Step 1 | `1-0-convert-to-html` | One `story/original/<chapter>.md` → HTML |
| `entities.html` missing, or any HTML chapter not in `data-chapters-scanned` | `1-1-entity-manifest` | Scan **one** chapter (`#pipeline-next-1-1` / `data-next-chapter`) |
| Step 1.1 complete and any appearance `<li>` lacks `data-slice-done="true"` | `1-2-entity-prompts` | **One** entity×chapter slice (`#pipeline-next-1-2` / `data-next-entity` + `data-next-chapter`) |
| Step 1.2 complete and a chapter lacks `story/adapted/<chapter>-screenplay.html` | `2-adapt-screenplay` | **One** screenplay chapter (first missing in slug order) |

4. Read that skill’s `SKILL.md`, do **only** that unit, update manifest **pipeline-next** lines and `data-next-*` before stopping.
5. **Context limit:** for Step 1.1 and 1.2, read **only** the single chapter HTML named in pipeline-next—not other chapters, not the full book.

If pipeline-next text disagrees with the table, fix the manifest when you write and prefer the table logic for routing.

---

## Early pipeline

| Step | Skill | Output |
|------|-------|--------|
| 1 | `1-0-convert-to-html` | `story/html/<chapter>.html` |
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
| `assets/screenplay.css` | Shared stylesheet for all pipeline HTML artifacts |
| `.pi/mypi/agents/stan-lee.yml` | Stan-lee preset |
| `.pi/skills/<n>-<name>/` | Step skills |

---

## Link rules

- Artifacts use real HTML links, not wiki links.
- Use relative `href` values so files work in a browser from the local filesystem.
- Generated source paragraphs use stable IDs like `p-001`; provenance links should point to `../html/<chapter>.html#p-001` or equivalent relative paths.
- **Project manifest** (`story/adapted/entities.html`): `data-chapters-scanned` lists chapters merged in Step 1.1. Each entity row has **Appearances** (`ul.chapter-refs` with `data-chapter` per chapter where the entity is **named or referred to** in prose). Top of manifest: `p.pipeline-next` lines and `data-next-*` on `<main>` — agents update these every run so the next unit of work is obvious.
- **Name cells:** plain text until Step 1.2 creates `prompts/<slug>/<slug>-base.html`, then link (entity opened—not “all appearances done”).
- **Appearance refs:** pending slices link to source HTML only; `data-slice-done="true"` links to `#evidence-<chapter>` on the prompt page plus source `p-xxx` in parentheses.
- **Entity prompt pages:** `section.appearances` at the top mirrors the manifest appearance list; keep `data-chapter` and `data-slice-done` in sync with the manifest on every Step 1.2 run.
- Screenplay (per chapter) links to `entities.html` and to prompt pages when they exist.
- **Step 1.1 complete:** every `story/html/<chapter>.html` slug is in `data-chapters-scanned`.
- **Step 1.2 complete:** every `chapter-refs` item has `data-slice-done="true"`, matching `section.appearances` on the prompt page, and a non-empty evidence section for that chapter.
- **Retired:** `story/adapted/<chapter>-entities.html` — do not create.

---

## Core conventions

- Filenames: kebab-case slugs.
- User originals remain Markdown under `story/original/`; generated adaptation artifacts are HTML.
- HTML should be simple, valid, and template-driven. Link the shared stylesheet `assets/screenplay.css` from every pipeline HTML file (use the correct relative `href` for that file’s depth). Do not use inline CSS or JavaScript in artifacts.
- Stylesheet `href` by location: `story/html/` and `story/adapted/` → `../../assets/screenplay.css`; `prompts/<entity>/` → `../../assets/screenplay.css`; repo-root `index.html` → `assets/screenplay.css`.
- Use semantic classes for screenplay structure (`scene`, `scene-heading`, `action`, `character`, `dialogue`).
- Update this file when cross-cutting structure changes; update the relevant step skill when step behavior changes.

---

## HTML contracts (summary)

Detailed templates and examples are in the step skills.

| Artifact | Step skill |
|----------|-------------|
| `story/html/<chapter>.html` | `1-0-convert-to-html` |
| `story/adapted/entities.html` | `1-1-entity-manifest` |
| `prompts/<entity>/<entity>-base.html` | `1-2-entity-prompts` |
| `story/adapted/<chapter>-screenplay.html` | `2-adapt-screenplay` |

---

## Project index

Run from the repository root:

```bash
./read
```

Regenerates `index.html` with links to HTML artifacts on disk (chapters, manifest, entity prompts, screenplays) and opens it in a browser. Use `./read --no-open` to generate without launching. Pipeline progress is not tracked on the index — agents infer step state from artifacts (especially `story/adapted/entities.html`).

---

## Preset

**stan-lee** (`.pi/mypi/agents/stan-lee.yml`): comic adaptation assistant. Project context includes this file via `includeContextFiles`. **`continue`** alone is valid; the agent routes from `entities.html` and reads the matching skill under `.pi/skills/`.
