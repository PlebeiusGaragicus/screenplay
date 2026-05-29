---
name: 2b-scene-screenplay
description: Write the screenplay section of one scene pack HTML. Step 2b; one scene per run.
---

# Step 2b — Scene screenplay (one scene)

## Bare continue

When the user says **`continue`** and AGENTS.md routes here:

1. Open `story/adapted/scenes.html` and read `#pipeline-next-2b` / `data-next-scene`.
2. Pick the first manifest row with `data-screenplay-done="false"` (lowest `data-scene-order`) if pipeline-next is stale.
3. Read **only** the source chapter HTML paragraphs listed on that row (`data-source-chapter`, `data-source-paragraphs`) and `entities.html` for entity slugs.
4. Create or update `story/scenes/<slug>.html` with header + `<section class="screenplay">` only—no blocking or moments yet.
5. Set `data-screenplay-done="true"` on the manifest row; update pack link and pipeline-next; then **stop**.

## Persist artifacts (mandatory)

- `write` or `edit` the scene pack and `scenes.html`; re-read both before claiming done.

## Goal

Adapt one scene’s prose into semantic **screenplay HTML** inside a scene pack. This is the script anchor for later moment tuples (Step 2c).

## Inputs

- `story/adapted/scenes.html` (target row)
- `story/html/<chapter>.html` (paragraph span only)
- `story/adapted/entities.html`

## Output

- `story/scenes/<scene-slug>.html` (screenplay section complete)
- Manifest row: `data-screenplay-done="true"`, pack cell linked

## Rules

- Use semantic classes: `scene-heading`, `action`, `character`, `dialogue` inside `<section class="screenplay">`.
- Link entity mentions to `../../prompts/<slug>/<slug>-base.html` when those pages exist.
- Link provenance to `../html/<chapter>.html#p-xxx` where helpful.
- Do not add `<article class="moment-card">` or blocking sections in this step.
- Do not invent entities absent from the manifest unless the user approves updating Step 1b/1c first.

## Scene pack template (screenplay-only pass)

Create the file if missing; on update, preserve `<head>` and stylesheet link.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>INT. COMPUTER LAB - NIGHT</title>
  <link rel="stylesheet" href="../../assets/screenplay.css">
</head>
<body>
  <main class="scene-pack"
        data-scene-slug="0-prologue-computer-lab-night"
        data-scene-order="1"
        data-source-chapter="0-prologue"
        data-source-paragraphs="p-001,p-002,p-003"
        data-moments-complete="false">

    <header class="scene-pack-meta">
      <h1>INT. COMPUTER LAB - NIGHT</h1>
      <p class="slug"><code>0-prologue-computer-lab-night</code></p>
      <p>Source:
        <a href="../html/0-prologue.html#p-001">0-prologue</a>
        <span class="source-refs">p-001–p-003</span>
      </p>
      <p>Manifest: <a href="../adapted/scenes.html#scene-0-prologue-computer-lab-night">scenes.html</a>
        · <a href="../adapted/entities.html">entities</a></p>
    </header>

    <section class="screenplay" id="screenplay" aria-label="Screenplay">
      <h2 class="scene-heading">INT. COMPUTER LAB - NIGHT</h2>
      <p class="action">
        <a href="../../prompts/james/james-base.html">James</a> sits at a terminal.
      </p>
      <p class="character">JAMES</p>
      <p class="dialogue">I'm still not sure I want to play something so pink and purple.</p>
    </section>

    <section class="blocking-layout" id="blocking-layout" aria-label="Blocking and floor plan">
      <h2>Blocking &amp; layout</h2>
      <p class="muted">Added in Step 2c with the first moment card.</p>
    </section>

    <section class="moments" id="moments" aria-label="Moments and storyboard">
      <h2>Moments</h2>
      <p class="muted">Moment cards added one per run in Step 2c.</p>
    </section>
  </main>
</body>
</html>
```

## Manifest updates

- Pack cell: `<a href="../scenes/<slug>.html">Scene pack</a>`
- Screenplay cell: `<span class="done">done</span>` or link to `#screenplay`
- Set `data-screenplay-done="true"` on `<tr id="scene-…">`

Refresh `#pipeline-next-2b` to the next scene with `data-screenplay-done="false"`, or `Step 2b: complete`.

Refresh `#pipeline-next-2c` to the first scene with screenplay done and `data-moments-complete="false"`.

## Done when

Scene pack exists with a complete `<section class="screenplay">`; manifest row marked done; pipeline-next updated.

## User Input

**User:** `$@`

---
