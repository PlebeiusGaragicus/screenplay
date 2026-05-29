---
name: 2a-identify-scenes
description: Index scenes in one chapter into the project scene manifest. Step 2a; bare continue after Step 1c, one chapter per run.
---

# Step 2a — Identify scenes (one chapter)

## Bare continue

When the user says **`continue`** and AGENTS.md routes here:

1. Open `story/adapted/scenes.html` (create from template below if missing).
2. Read `data-chapters-scene-indexed` and `#pipeline-next-2a` — pick the first `story/html/<chapter>.html` slug **not** in `data-chapters-scene-indexed`.
3. Read **only** that chapter’s source HTML and `story/adapted/entities.html` (entity slugs for cross-links later—not full prompt corpora).
4. Merge every **scene** you find in that chapter into the manifest table, update pipeline-next, then **stop**.

Do not create scene packs or moment cards in this run (Steps 2b / 2c).

## Persist artifacts (mandatory)

- You **must** `write` or `edit` `story/adapted/scenes.html` before ending the run.
- **Re-read** the file and confirm this chapter’s slug appears in `data-chapters-scene-indexed`.

## Goal

Turn one source chapter into **scene rows** in the project scene manifest. A **scene** is one continuous time/place unit (screenplay scene-heading granularity): location change, time jump, or a clear dramatic reset.

Chapters remain the **ingest** boundary; **scenes** are the adaptation boundary for Steps 2b and 2c.

## Inputs

- `story/html/<chapter>.html`
- `story/adapted/entities.html` (optional: align scene slugs with scene entities when obvious)
- `story/adapted/scenes.html` (create if missing)

## Output

- Updated `story/adapted/scenes.html`
- One `<tr id="scene-<slug>">` per scene discovered in this chapter
- Chapter slug appended to `data-chapters-scene-indexed`

## Scene slug rules

- Kebab-case, globally unique: `<source-chapter>-<descriptor>` (e.g. `0-prologue-computer-lab-night` from `INT. COMPUTER LAB - NIGHT`).
- `id="scene-<slug>"` on the row; pack file will be `story/scenes/<slug>.html`.
- `data-scene-order` — integer, monotonic across the **whole project** (append after the current max in the table).
- `data-source-chapter`, `data-source-paragraphs` (comma-separated `p-xxx` spans covering the scene).

## Scene boundary heuristics

Split a new scene when the prose implies:

- `INT.` / `EXT.` / location or time change
- Hard cut (“Later,” “Meanwhile,” new POV with new setting)
- A visitable environment entity in the manifest often maps 1:1 to a scene row

Do **not** split into moment/shot cards here—that is Step 2c.

## Manifest row template

Add inside `<tbody>` of `#scenes table`:

```html
<tr id="scene-0-prologue-computer-lab-night"
    data-source-chapter="0-prologue"
    data-scene-order="1"
    data-source-paragraphs="p-001,p-002,p-003"
    data-screenplay-done="false"
    data-moments-complete="false">
  <td>
    <strong>INT. COMPUTER LAB - NIGHT</strong><br>
    <code>0-prologue-computer-lab-night</code>
  </td>
  <td>
    <a href="../html/0-prologue.html#p-001">0-prologue</a>
    <span class="source-refs">p-001–p-003</span>
  </td>
  <td class="pack-cell"><span class="muted">pending</span></td>
  <td class="screenplay-cell"><span class="muted">pending</span></td>
  <td>
    <ul class="moment-refs" data-scene-slug="0-prologue-computer-lab-night"></ul>
  </td>
</tr>
```

When `story/scenes/<slug>.html` exists, replace pack cell with `<a href="../scenes/<slug>.html">Scene pack</a>`.

## Pipeline next (update every run)

At the top of `scenes.html`:

```html
<p class="pipeline-next" id="pipeline-next-2a" data-step="2a">Step 2a next: index scenes in <strong>0-prologue</strong> — read <code>story/html/0-prologue.html</code></p>
<p class="pipeline-next" id="pipeline-next-2b" data-step="2b">Step 2b next: screenplay for <strong>0-prologue-computer-lab-night</strong></p>
<p class="pipeline-next" id="pipeline-next-2c" data-step="2c">Step 2c: waiting for scene screenplays</p>
```

On `<main class="scene-manifest">`:

- `data-chapters-scene-indexed` — comma-separated chapter slugs with completed 2a
- `data-next-step` — `2a`, `2b`, or `2c` for the step you are advancing
- `data-next-chapter` — for 2a
- `data-next-scene` — for 2b and 2c
- `data-next-moment` — for 2c only (moment slug, e.g. `0-prologue-computer-lab-night-m01`)

When **2a is complete** for the project, set `#pipeline-next-2a` to `Step 2a: complete`.

After each 2a run, refresh `#pipeline-next-2b` to the first scene row with `data-screenplay-done="false"` (lowest `data-scene-order`).

## Scenes manifest skeleton

Use when creating the file; preserve structure and stylesheet link when merging.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Scene Manifest</title>
  <link rel="stylesheet" href="../../assets/screenplay.css">
</head>
<body>
  <main class="scene-manifest"
        data-chapters-scene-indexed=""
        data-next-step="2a"
        data-next-chapter="0-prologue"
        data-next-scene=""
        data-next-moment="">
    <h1>Scene Manifest</h1>
    <p>Entities: <a href="entities.html">story/adapted/entities.html</a></p>
    <p class="pipeline-next" id="pipeline-next-2a" data-step="2a">Step 2a next: index scenes in <strong>0-prologue</strong></p>
    <p class="pipeline-next" id="pipeline-next-2b" data-step="2b">Step 2b: waiting for scene index</p>
    <p class="pipeline-next" id="pipeline-next-2c" data-step="2c">Step 2c: waiting for scene screenplays</p>

    <section id="scenes">
      <h2>Scenes</h2>
      <table class="scene-table">
        <thead>
          <tr>
            <th>Scene</th>
            <th>Source</th>
            <th>Pack</th>
            <th>Screenplay</th>
            <th>Moments</th>
          </tr>
        </thead>
        <tbody></tbody>
      </table>
    </section>
  </main>
</body>
</html>
```

## Done when (single run)

This chapter’s slug is in `data-chapters-scene-indexed`; every scene in the chapter has a manifest row with correct `data-source-paragraphs`; pipeline-next lines updated.

## Done when (project)

Every `story/html/*.html` chapter slug is in `data-chapters-scene-indexed`.

## User Input

**User:** `$@`

---
