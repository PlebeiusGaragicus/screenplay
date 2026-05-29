---
name: 2c-scene-moment
description: Add one moment card (shot tuple) to a scene pack. Step 2c; one scene × one moment per run.
---

# Step 2c — Scene moment (one moment card)

## Bare continue

When the user says **`continue`** and AGENTS.md routes here:

1. Open `story/adapted/scenes.html` and read `#pipeline-next-2c`, `data-next-scene`, `data-next-moment`.
2. Pick work in this order if pipeline-next is stale:
   - First manifest row with `data-screenplay-done="true"` and `data-moments-complete="false"` (lowest `data-scene-order`).
   - Within that scene, the next moment: first `<li>` in `ul.moment-refs` without `data-moment-done="true"`, or the next sequential `…-mNN` if no list items yet.
3. Read **only** that scene’s pack `#screenplay` and the source paragraph span from the manifest row.
4. Append **one** `<article class="moment-card">` to `story/scenes/<slug>.html` and flesh out `#blocking-layout` on the first moment for that scene (floor plan + blocking table shared by the scene).
5. Sync manifest `ul.moment-refs`; if the scene has full storyboard coverage, set `data-moments-complete="true"` on manifest row and `<main class="scene-pack">`.
6. Update pipeline-next; then **stop**.

## Persist artifacts (mandatory)

- `write` or `edit` scene pack and `scenes.html`; re-read both before claiming done.

## Goal

Add one **moment** (storyboard chunk / shot tuple) to a scene pack: script anchor, storyboard sketch, specs, blocking, audio. See `docs/shot-master-tuple.md` for the creative model.

**Moment** = one drawable beat (often one panel). Multiple moments per scene are normal; do not add more than **one** moment card per run.

## Inputs

- `story/scenes/<scene-slug>.html` (must have screenplay section)
- `story/adapted/scenes.html`
- Source HTML paragraphs for that scene only

## Output

- Updated scene pack with one new `article.moment-card`
- Updated `ul.moment-refs` on the manifest row
- Optional: `data-moment-global` on the moment (project-wide counter; increment from highest existing)

## Moment slug rules

- `id="<scene-slug>-m01"` on `<article class="moment-card">` (zero-padded two digits: m01, m02, …).
- `data-moment-order` on the article (1-based within the scene).
- `data-moment-done="true"` on the matching `<li>` in the manifest.

## Moment card template

Append inside `<section class="moments">`:

```html
<article class="moment-card" id="0-prologue-computer-lab-night-m01"
         data-moment-order="1"
         data-moment-global="1">
  <header class="moment-card-header">
    <h3>Moment 1 — The lab</h3>
    <p class="script-anchor">Script: <a href="#screenplay">screenplay</a>
      · <a href="../html/0-prologue.html#p-001">p-001</a></p>
  </header>

  <div class="moment-grid">
    <figure class="storyboard-panel">
      <figcaption>Storyboard</figcaption>
      <p class="storyboard-sketch">Wide: James small at terminal, pink UI glow on face…</p>
    </figure>
    <dl class="shot-specs">
      <dt>Type</dt><dd>WS</dd>
      <dt>Angle</dt><dd>Eye level, static</dd>
      <dt>Lens</dt><dd>24mm</dd>
      <dt>Movement</dt><dd>None</dd>
    </dl>
  </div>

  <section class="moment-blocking" aria-label="Blocking for this moment">
    <h4>Blocking</h4>
    <ul>
      <li><strong>James:</strong> seated at terminal, slumped.</li>
    </ul>
    <p class="floor-plan-ref">Camera: corner <code>C1</code> — see <a href="#blocking-layout">floor plan</a>.</p>
  </section>

  <section class="moment-audio" aria-label="Audio for this moment">
    <h4>Audio / SFX</h4>
    <ul>
      <li>Ambient: computer fan hum.</li>
    </ul>
  </section>
</article>
```

## Blocking layout (first moment in a scene)

Replace the placeholder in `#blocking-layout` when adding the **first** moment for that scene:

```html
<section class="blocking-layout" id="blocking-layout" aria-label="Blocking and floor plan">
  <h2>Blocking &amp; layout</h2>
  <pre class="floor-plan" aria-label="Top-down diagram">[ KEY: (C) Camera (J) James [T] Terminal ]

       _______________________________
      |  [T]     (J)                  |
      |           \                  |
      |            \   (C1) Wide     |
      |________________________________|
</pre>
  <table class="blocking-table">
    <thead><tr><th>Mark</th><th>Description</th></tr></thead>
    <tbody>
      <tr><td><code>C1</code></td><td>Wide establish — corner opposite terminal</td></tr>
    </tbody>
  </table>
</section>
```

Later moments reference marks in the floor plan; extend the table when new camera positions appear.

## Coverage / scene complete

After each moment, ask: can an editor/storyboard artist cover the screenplay beat with existing moments?

- If **no** — leave `data-moments-complete="false"`; set `#pipeline-next-2c` to the same scene and the next moment slug.
- If **yes** — set `data-moments-complete="true"` on manifest `<tr>` and `<main class="scene-pack">`; advance `#pipeline-next-2c` to the next scene.

Typical drama scene: 3–8 moments; do not pad beyond coverage.

## Manifest moment ref

```html
<li data-moment-id="0-prologue-computer-lab-night-m01"
    data-moment-done="true">
  <a href="../scenes/0-prologue-computer-lab-night.html#0-prologue-computer-lab-night-m01">m01 — The lab</a>
</li>
```

Pending moments use `data-moment-done="false"` and link only to the pack without hash until done.

## Done when

Exactly one new moment card is on disk; manifest `moment-refs` synced; pipeline-next reflects the next moment or next scene.

## Done when (project)

Every scene row has `data-screenplay-done="true"` and `data-moments-complete="true"`.

## User Input

**User:** `$@`

---
