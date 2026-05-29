# Comic adaptation project

Turn original prose into an AI-generated comic: source chapters → entity bible → adapted screenplay → reference art → storyboard panels → generated images → (future) comic assembly.

**Working mode:** Human-led, one phase (and usually one chapter) at a time. The **stan-lee** preset knows the full pipeline below but only works the phase the user names.

---

## How to work (stan-lee)

1. The user states **phase** and **chapter** (e.g. Phase 2, `0-prologue`).
2. Work only that scope; write artifacts under the paths below.
3. End with: what changed, open questions, and a **suggested** next step — do not start the next phase without confirmation.
4. If a request spans phases, ask which phase to do first.

---

## Phases

### Phase 1 — Ingest original *(iterating)*

**Goal:** Clean, chapter-scoped source files.

**Inputs:** Raw manuscript (any format the user provides).

**Outputs:** `story/original/<chapter>.md` — one file per chapter; stable names (e.g. `0-prologue.md`).

**Rules:**

- Preserve author wording; light cleanup only (typography, obvious OCR fixes).
- Do not adapt to screenplay yet.
- Do not write image prompts yet.

**Done when:** The chapter file reads cleanly and matches an agreed chapter boundary.

---

### Phase 2 — Entity bible *(iterating)*

**Goal:** Reusable descriptions for characters, locations, props, and project style — grounded in prose, not final panel prompts.

**Outputs:**

- `prompts/characters/<slug>/<slug>.md`
- `prompts/locations/<slug>/<slug>.md`
- `prompts/props/<slug>/<slug>.md` (when needed)
- `prompts/styles/<slug>/<slug>.md`

**Rules:**

- Prefer language close to the source; fill in only where prose is vague, without contradicting the author.
- Record **provenance**: chapter file plus approximate location (section heading or line range).
- Track **variants** (wounded, costume, under influence, etc.) in frontmatter or subsections — do not overwrite the base look silently.
- These files are the bible for adaptation and storyboards, not necessarily the final string sent to an image API.

**Done when:** Every named entity in the chapter has a prompt file, or the user accepts a deferred list.

---

### Phase 3 — Adapt to screenplay *(iterating)*

**Goal:** Fountain-flavored screenplay per chapter, linked to the entity bible.

**Outputs:** `story/adapted/<chapter>.md`

**Rules:**

- Scene headings, action, dialogue; wiki links to prompt files, e.g. `[[prompts/characters/james/james.md]]`.
- Frontmatter lists entities used in that chapter; every path must exist.
- Work chapter by chapter with the user.

**Done when:** The adapted file matches the chapter and all `[[prompts/...]]` links resolve.

---

### Phase 4 — Reference images *(iterating, manual-heavy)*

**Goal:** Consistent look for characters and locations before panel work.

**Outputs:** e.g. `prompts/characters/james/james-ref.png` next to the `.md`.

**Rules:**

- Keep `.md` as the source of truth; iterate descriptions there first.
- The user approves refs before expecting strong panel consistency.

**Done when:** The user is satisfied with refs needed for the current chapter.

---

### Phase 5 — Storyboard panels *(iterating)*

**Goal:** One markdown file per panel, combining the bible and a moment from the screenplay.

**Outputs:** `storyboard/<page>/<order>-<slug>.md`

**Rules:**

- YAML frontmatter: `page`, `panel`, `chapter`, `scene`, and image parameters (`model`, `aspect_ratio`, `resolution`, etc.) as needed.
- Body: wiki links to entities plus shot description (action, camera, lighting).
- Scope to pages and chapters the user requests.

**Done when:** Panel files exist for the agreed beats and links resolve.

---

### Phase 6 — Panel images *(iterating)*

**Goal:** Generate PNGs from storyboard files when the user asks.

**Outputs:** `images/storyboard/`

**Rules:**

- Use the **grok-txt2img** skill (bash) on a specific storyboard `.md` path.
- The user reviews outputs and may regenerate; image files are managed by the user.

**Done when:** The user has acceptable images for the panels they requested.

---

### Phase 7 — Assemble comic *(not designed yet)*

Layout, lettering, and export. Do not start unless the user explicitly asks for Phase 7.

---

## Repository layout

| Path | Purpose |
|------|---------|
| `story/original/` | Source chapters |
| `story/adapted/` | Screenplays |
| `prompts/{characters,locations,props,styles}/<slug>/<slug>.md` | Entity bible |
| `prompts/**/<slug>-ref.png` | Optional reference images |
| `storyboard/<page>/` | Panel prompt files |
| `images/storyboard/` | Generated panels |
| `.pi/mypi/agents/stan-lee.yml` | Stan-lee preset overlay |
| `.pi/skills/grok-txt2img/` | txt2img skill |

---

## Frontmatter

### `story/original/<chapter>.md`

```yaml
---
title: Chapter title
---
```

### `prompts/<type>/<slug>/<slug>.md`

```yaml
---
name: Display Name
type: character | location | prop | style
sources:
  - file: story/original/0-prologue.md
    note: optional line or section
variants: []   # optional; describe alternate looks when needed
---
```

### `story/adapted/<chapter>.md`

```yaml
---
title: Chapter title
characters: []
locations: []
prompts: []   # repo-relative paths under prompts/, must exist
---
```

### `storyboard/<page>/<order>-<slug>.md`

```yaml
---
page: 1
panel: 0
chapter: 0-prologue
scene: "INT. LOCATION - beat"
model: grok-imagine-image-quality
aspect_ratio: "16:9"
resolution: 1k
---
```

Use keys the **grok-txt2img** script actually reads (`model`, `aspect_ratio`, `size`, `resolution`). Other keys are documentation until the script supports them.

---

## Link rules

- Wiki links use `[[prompts/...]]` paths relative to the repository root.
- Every link in `story/adapted/` and `storyboard/` must resolve to an existing file before that chapter’s Phase 3 or Phase 5 is considered complete.
- Nested layout: `prompts/characters/james/james.md`, not a flat `prompts/characters/james.md`.

---

## Core conventions

- Filenames: kebab-case. YAML keys: Title Case where shown above.
- `story/*.md` and `prompts/*.md`: YAML frontmatter at top; Fountain-style body where applicable.
- Generated images live under `images/`; the user decides what to keep or remove.
- Update this file when a phase contract changes.

---

## Preset and skill

**stan-lee** (`.pi/mypi/agents/stan-lee.yml`): comic adaptation assistant. Project context includes this file via `includeContextFiles`. Follow the phase rules above; the human leads.

**grok-txt2img** (`.pi/skills/grok-txt2img/`): Phase 6 only. Run via bash:

```bash
.pi/skills/grok-txt2img/scripts/grok-txt2img storyboard/page-1/0-lab-wide.md
```

Requires `GROK_API_KEY` or `XAI_API_KEY` in a project-root `.env` file.
