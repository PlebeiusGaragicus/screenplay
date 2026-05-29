# Comic adaptation project

Turn user-provided prose into a navigable HTML adaptation workspace: Markdown originals → source HTML → entity manifest → screenplay HTML → entity prompt pages → later visual phases.

**Working mode:** Human-led, one step (and usually one chapter) at a time. The **stan-lee** preset routes to the numbered skill for the step the user names.

---

## Prerequisites

The user supplies organized Markdown source chapters before agent work begins:

- One file per chapter under `story/original/<chapter>.md` (kebab-case slug, e.g. `0-prologue.md`).
- Optional YAML frontmatter with `title` only.
- No agent ingest phase — do not rewrite or split manuscripts unless the user explicitly asks outside the pipeline.

---

## How to work (stan-lee)

1. The user states **step** and **chapter** (e.g. Step 1.1, `0-prologue`).
2. Read `.pi/skills/<n>-<name>/SKILL.md` for that step and follow it.
3. Structural rules in this file always apply.
4. End with: what changed, open questions, and a **suggested** next step — do not start the next step without confirmation.
5. If a request spans steps, ask which step to do first.

---

## Early pipeline

| Step | Skill | Output |
|------|-------|--------|
| 1 | `1-convert-originals` | `story/html/<chapter>.html` |
| 1.1 | `1.1-entity-manifest` | `story/adapted/<chapter>-entities.html` |
| 1.2 | `1.2-entity-prompts` | `prompts/<entity>/<entity>-base.html` |
| 2 | `2-adapt-screenplay` | `story/adapted/<chapter>-screenplay.html` |

Later visual phases are intentionally out of scope for this early pipeline. Step procedures, templates, and “done when” criteria live in each skill, not here.

---

## Repository layout

| Path | Purpose |
|------|---------|
| `story/original/` | User-provided Markdown chapters (prerequisite) |
| `story/html/` | Generated source HTML with stable paragraph anchors |
| `story/adapted/` | Entity manifests and screenplay HTML |
| `prompts/<entity>/` | Entity prompt HTML pages and later reference assets |
| `.pi/mypi/agents/stan-lee.yml` | Stan-lee preset |
| `.pi/skills/<n>-<name>/` | Step skills |

---

## Link rules

- Artifacts use real HTML links, not wiki links.
- Use relative `href` values so files work in a browser from the local filesystem.
- Generated source paragraphs use stable IDs like `p-001`; provenance links should point to `../html/<chapter>.html#p-001` or equivalent relative paths.
- Manifest rows, screenplay entity mentions, and entity prompt pages should link to each other when the target path is known.
- Every internal `href` under `story/` and `prompts/` should resolve before a step is complete.

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
| `story/adapted/<chapter>-entities.html` | `1.1-entity-manifest` |
| `prompts/<entity>/<entity>-base.html` | `1.2-entity-prompts` |
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
