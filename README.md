# Screenplay

Adapt prose chapters into HTML: source text, entity catalog, prompt pages, then scene packs (screenplay + blocking + storyboard moments).

**Prerequisite:** Markdown chapters in `story/original/<chapter>.md` (one file per chapter).

**Agent mode:** Say **`continue`** once per unit. The agent reads `story/adapted/entities.html` (and `scenes.html` after Step 1c) and runs exactly one step below.

**Navigation:** `./read` regenerates `index.html` and opens it in a browser.

Full rules: [AGENTS.md](AGENTS.md). Step procedures: `.pi/skills/*/SKILL.md`.

---

## Pipeline

| Step | Skill | One unit | Output |
|------|-------|----------|--------|
| **1a** | `1a-convert-to-html` | One chapter | `story/html/<chapter>.html` |
| **1b** | `1b-entity-manifest` | Scan one chapter | `story/adapted/entities.html` |
| **1c** | `1c-entity-prompts` | One entity × one chapter | `prompts/<entity>/<entity>-base.html` |
| **2a** | `2a-identify-scenes` | Index scenes in one chapter | `story/adapted/scenes.html` |
| **2b** | `2b-scene-screenplay` | One scene | `story/scenes/<scene-slug>.html` (screenplay section) |
| **2c** | `2c-scene-moment` | One moment in one scene | Same pack — one moment card + blocking |

Steps run in order. Steps 2a–2c start after Step 1c is complete for the project.

Moment / shot design: [docs/shot-master-tuple.md](docs/shot-master-tuple.md).
