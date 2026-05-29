# Comic adaptation project

Turn user-provided prose into a navigable HTML adaptation workspace: Markdown originals → source HTML → entity manifest → entity prompt pages → **scene manifest → scene packs (screenplay + moments)** → later visual phases.

**Working mode:** One unit of work per run. The user may name a step explicitly, or say **`continue`** alone—the agent reads manifests and does exactly **one** next unit (one chapter scan, one entity×chapter slice, one chapter scene index, one scene screenplay, or one moment card).

---

## Prerequisites

The user supplies organized Markdown source chapters before agent work begins:

- One file per chapter under `story/original/<chapter>.md` (kebab-case slug, e.g. `0-prologue.md`).
- Optional YAML frontmatter with `title` only.
- No agent ingest phase — do not rewrite or split manuscripts unless the user explicitly asks outside the pipeline.

---

## How to work (stan-lee)

1. If the user says **`continue`** (only), follow **Bare continue** below—do not ask which step.
2. Otherwise the user may state **step** and optionally **chapter** or **scene** (e.g. Step 2b, `0-prologue-computer-lab-night`).
3. Read `.pi/skills/<n>-<name>/SKILL.md` for that step and follow it.
4. Structural rules in this file always apply.
5. **Persist:** use `write` or `edit` on every output file you change; re-read the file to verify before claiming done. Never report a merge or slice as complete from chat alone.
6. End with: what changed (paths only if written), open questions, and **suggested next step** (usually “say **continue**”). Do not start a second unit in the same run.
7. If a request spans steps, ask which step to do first.

---

## Bare continue

Fresh context starts here—no chat history required.

1. **Open** `story/adapted/entities.html` when it exists; otherwise infer Step 1a or 1b from `story/html/` and `story/original/`.
2. **Read first** (top of manifests, in order):
   - `#pipeline-next-1b` and `data-chapters-scanned` on `<main class="entity-manifest">`
   - `#pipeline-next-1c` and `data-next-entity`, `data-next-chapter` on entity manifest
   - When Step 1c is complete, open `story/adapted/scenes.html` and read `#pipeline-next-2a`, `#pipeline-next-2b`, `#pipeline-next-2c` and `data-chapters-scene-indexed`, `data-next-scene`, `data-next-moment`
3. **Pick exactly one skill** (first match wins):

| Condition | Skill | One unit |
|-----------|--------|----------|
| Some `story/html/*.html` missing | `1a-convert-to-html` | One `story/original/<chapter>.md` → HTML |
| `entities.html` missing, or any HTML chapter not in `data-chapters-scanned` | `1b-entity-manifest` | Scan **one** chapter (`#pipeline-next-1b` / `data-next-chapter`) |
| Step 1b complete and any appearance `<li>` lacks `data-slice-done="true"` | `1c-entity-prompts` | **One** entity×chapter slice (`#pipeline-next-1c` / `data-next-entity` + `data-next-chapter`) |
| Step 1c complete and any chapter not in `data-chapters-scene-indexed` | `2a-identify-scenes` | Index scenes in **one** chapter |
| Scene row with `data-screenplay-done="false"` | `2b-scene-screenplay` | **One** scene pack screenplay section |
| Scene row with `data-screenplay-done="true"` and `data-moments-complete="false"` | `2c-scene-moment` | **One** moment card in that scene pack |

4. Read that skill’s `SKILL.md`, do **only** that unit, update manifest **pipeline-next** lines and `data-next-*` before stopping.
5. **Context limit:** for Steps 1b, 1c, and 2a, read **only** the single chapter HTML named in pipeline-next—not other chapters, not the full book. For 2b and 2c, read only the source paragraphs and pack for the named scene.

If pipeline-next text disagrees with the table, fix the manifest when you write and prefer the table logic for routing.

---

## Early pipeline

| Step | Skill | Output |
|------|-------|--------|
| 1a | `1a-convert-to-html` | `story/html/<chapter>.html` |
| 1b | `1b-entity-manifest` | `story/adapted/entities.html` (merge one chapter per run) |
| 1c | `1c-entity-prompts` | `prompts/<entity>/<entity>-base.html` (one entity×chapter slice per run) |
| 2a | `2a-identify-scenes` | `story/adapted/scenes.html` (scene rows from one chapter) |
| 2b | `2b-scene-screenplay` | `story/scenes/<scene-slug>.html` (screenplay section) |
| 2c | `2c-scene-moment` | Same pack — one moment card + blocking per run |

Later visual phases are intentionally out of scope for this early pipeline. Step procedures, templates, and “done when” criteria live in each skill, not here. Shot/moment design notes: `docs/shot-master-tuple.md`.

---

## Repository layout

| Path | Purpose |
|------|---------|
| `story/original/` | User-provided Markdown chapters (prerequisite) |
| `story/html/` | Generated source HTML with stable paragraph anchors |
| `story/adapted/` | Entity manifest, scene manifest |
| `story/adapted/entities.html` | Project-wide entity catalog (Step 1b / 1c) |
| `story/adapted/scenes.html` | Project-wide scene index (Step 2a–2c) |
| `story/scenes/` | Scene pack HTML (screenplay + blocking + moment cards) |
| `prompts/<entity>/` | Entity prompt HTML pages and later reference assets |
| `assets/screenplay.css` | Shared stylesheet for all pipeline HTML artifacts |
| `docs/shot-master-tuple.md` | Creative model for moment / shot tuples |
| `.pi/mypi/agents/stan-lee.yml` | Stan-lee preset |
| `.pi/skills/<n>-<name>/` | Step skills |

**Retired:** `story/adapted/<chapter>-screenplay.html` — do not create. Use scene packs instead.

---

## Link rules

- Artifacts use real HTML links, not wiki links.
- Use relative `href` values so files work in a browser from the local filesystem.
- Generated source paragraphs use stable IDs like `p-001`; provenance links should point to `../html/<chapter>.html#p-001` or equivalent relative paths.
- **Entity manifest** (`story/adapted/entities.html`): `data-chapters-scanned` lists chapters merged in Step 1b. Pipeline-next element ids: `#pipeline-next-1b`, `#pipeline-next-1c` with `data-step="1b"` / `data-step="1c"`. Each entity row has **Appearances** (`ul.chapter-refs`): **exactly one** `<li data-chapter="…">` per chapter where the entity is named or referred—not one `<li>` per paragraph. Link text is the chapter slug (e.g. `0-prologue`); paragraph ids (`p-001`) belong in Step 1c evidence, not as a long manifest list. Top of manifest: `p.pipeline-next` lines and `data-next-*` on `<main>` — agents update these every run so the next unit of work is obvious.
- **Scene manifest** (`story/adapted/scenes.html`): `data-chapters-scene-indexed` lists chapters indexed in Step 2a. Each scene row (`id="scene-<slug>"`) has source paragraph span, pack link, `data-screenplay-done`, `data-moments-complete`, and `ul.moment-refs` synced with the pack. Pipeline-next lines `#pipeline-next-2a`, `#pipeline-next-2b`, `#pipeline-next-2c`.
- **Scene packs** (`story/scenes/<slug>.html`): `<main class="scene-pack">` with `data-scene-slug`, `data-source-chapter`, `data-source-paragraphs`, `data-moments-complete`. Sections: `#screenplay`, `#blocking-layout`, `#moments` with `article.moment-card` per moment.
- **Name cells:** plain text until Step 1c creates `prompts/<slug>/<slug>-base.html`, then link (entity opened—not “all appearances done”).
- **Appearance refs:** one list item = one **(entity × chapter)** slice. Pending (Step 1b): link to chapter source HTML, chapter slug as label. Done (Step 1c): primary link to `#evidence-<chapter>` on the prompt page; optional single `p-xxx` in parentheses—not a paragraph inventory in Appearances.
- **Entity prompt pages:** `section.appearances` at the top mirrors the manifest appearance list; keep `data-chapter` and `data-slice-done` in sync with the manifest on every Step 1c run.
- Scene packs link to `entities.html`, `scenes.html`, source HTML, and prompt pages when they exist.
- **Step 1b complete:** every `story/html/<chapter>.html` slug is in `data-chapters-scanned`.
- **Step 1c complete:** every `chapter-refs` item has `data-slice-done="true"`, matching `section.appearances` on the prompt page, and a non-empty evidence section for that chapter.
- **Step 2a complete:** every HTML chapter slug is in `data-chapters-scene-indexed`.
- **Step 2b complete (per scene):** `data-screenplay-done="true"` and pack has `<section class="screenplay">`.
- **Step 2c complete (per scene):** `data-moments-complete="true"` and every planned moment has `data-moment-done="true"` on the manifest list matching pack cards.
- **Retired:** `story/adapted/<chapter>-entities.html` — do not create.

---

## Core conventions

- Filenames: kebab-case slugs.
- User originals remain Markdown under `story/original/`; generated adaptation artifacts are HTML.
- HTML should be simple, valid, and template-driven. Link the shared stylesheet `assets/screenplay.css` from every pipeline HTML file (use the correct relative `href` for that file’s depth). Do not use inline CSS or JavaScript in artifacts.
- Stylesheet `href` by location: `story/html/` and `story/adapted/` → `../../assets/screenplay.css`; `story/scenes/` → `../../assets/screenplay.css`; `prompts/<entity>/` → `../../assets/screenplay.css`; repo-root `index.html` → `assets/screenplay.css`.
- Use semantic classes for screenplay structure (`scene-heading`, `action`, `character`, `dialogue`) inside scene packs.
- **Scene** = one continuous time/place unit. **Moment** = one storyboard / shot tuple (see `docs/shot-master-tuple.md`).
- Update this file when cross-cutting structure changes; update the relevant step skill when step behavior changes.

---

## HTML contracts (summary)

Detailed templates and examples are in the step skills.

| Artifact | Step skill |
|----------|-------------|
| `story/html/<chapter>.html` | `1a-convert-to-html` |
| `story/adapted/entities.html` | `1b-entity-manifest` |
| `prompts/<entity>/<entity>-base.html` | `1c-entity-prompts` |
| `story/adapted/scenes.html` | `2a-identify-scenes` |
| `story/scenes/<scene-slug>.html` | `2b-scene-screenplay`, `2c-scene-moment` |

---

## Project index

Run from the repository root:

```bash
./read
```

Regenerates `index.html` with links to HTML artifacts on disk (chapters, manifests, scene packs, entity prompts) and opens it in a browser. Use `./read --no-open` to generate without launching. Pipeline progress is not tracked on the index — agents infer step state from manifests (`entities.html`, `scenes.html`).

---

## Preset

**stan-lee** (`.pi/mypi/agents/stan-lee.yml`): comic adaptation assistant. Project context includes this file via `includeContextFiles`. **`continue`** alone is valid; the agent routes from manifests and reads the matching skill under `.pi/skills/`.
