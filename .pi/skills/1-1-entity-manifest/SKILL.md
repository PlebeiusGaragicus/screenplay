---
name: 1-1-entity-manifest
description: Merge one chapter into the project entity manifest. Step 1.1; bare continue uses this when a chapter is not yet in data-chapters-scanned.
---

# Step 1.1 — Entity manifest (project-wide)

## Bare continue

When the user says **`continue`** and AGENTS.md routes here:

1. Open `story/adapted/entities.html` and read `#pipeline-next-1-1` / `data-next-chapter` (or derive the first HTML chapter missing from `data-chapters-scanned`).
2. Read **only** `story/html/<that-chapter>.html`.
3. Merge into `entities.html`, update pipeline-next, then **stop**.

Do not start Step 1.2 in the same run.

## Persist artifacts (mandatory)

- You **must** `write` or `edit` `story/adapted/entities.html` before ending the run.
- **Re-read** the file and confirm this chapter’s slug appears in `data-chapters-scanned`.
- Do not list this chapter in “what changed” unless the write succeeded and the attribute is present.

## Goal

Maintain one project manifest by scanning **one chapter per run** and merging entities into it. Record display names, slugs, and **appearances** (every chapter where the entity is named or referred to in prose). Do not create prompt pages or prompt links in Name cells—that is Step 1.2.

## Inputs

- `story/html/<chapter>.html` (the chapter to scan this run)
- `story/adapted/entities.html` (create from template if missing)

## Output

- Updated `story/adapted/entities.html`
- `data-chapters-scanned` on `<main>` appended with this chapter’s slug
- Updated **pipeline next** pointers (see below)

**Retired:** do not create or update `story/adapted/<chapter>-entities.html`.

## Pipeline next (update every run)

At the top of `entities.html`, keep two lines humans and agents read first:

```html
<p class="pipeline-next" id="pipeline-next-1-1" data-step="1.1">Step 1.1 next: scan <strong>1-opportunity</strong> — read <code>story/html/1-opportunity.html</code></p>
<p class="pipeline-next" id="pipeline-next-1-2" data-step="1.2">Step 1.2 next: <strong>james</strong> — <strong>0-prologue</strong> — read <code>story/html/0-prologue.html</code></p>
```

Also set on `<main class="entity-manifest">`:

- `data-next-step="1.1"` or `1.2` for the step you are advancing
- `data-next-chapter="<slug>"` — for 1.1, the chapter to scan; for 1.2, leave as set by the last 1.2 run or the skill’s walk order
- `data-next-entity="<slug>"` — only for 1.2 (omit or empty during 1.1-only runs)

When Step 1.1 is **complete** for the project, set `#pipeline-next-1-1` to `Step 1.1: complete` and clear `data-next-step` / `data-next-chapter` for 1.1 (or leave `data-next-step="1.2"` if 1.2 is active).

After each 1.1 run, refresh `#pipeline-next-1-2` to the current first pending 1.2 slice (same walk order as Step 1.2) so the manifest always shows what to do next.

## Continue workflow (Step 1.1)

1. List chapter slugs that have `story/html/<chapter>.html`, in sorted order.
2. Read `data-chapters-scanned` on `<main class="entity-manifest">` (comma-separated slugs; empty if missing).
3. **Next chapter** = first HTML chapter **not** in `data-chapters-scanned` (should match `#pipeline-next-1-1`).
4. If the user names a chapter explicitly, scan that chapter instead (still merge into the project manifest).
5. Extract entities from that chapter only; **merge** into `entities.html` (see merge rules). For **existing rows**, append an Appearances `<li>` for this chapter when the entity is mentioned—do not create duplicate scene/character rows.
6. Append the chapter slug to `data-chapters-scanned`.
7. Update pipeline-next lines and `data-next-*` on `<main>` (`write`/`edit`, then verify).
8. Process **one chapter**, then stop. Tell the user to say **continue** for the next unit.

**Step 1.1 complete** when every `story/html/*.html` chapter is listed in `data-chapters-scanned`.

## Manifest link contract (shared with Step 1.2)

| Element | Step 1.1 | Step 1.2 |
|---------|----------|----------|
| **Name** | Plain text only | Link to `../../prompts/<slug>/<slug>-base.html` when that file is **first created** |
| **Appearances** (`ul.chapter-refs`) | Source links per chapter mention | Mark slice done (see Step 1.2) |
| Row `id` | `entity-<slug>` | Unchanged |

**Appearances column (Step 1.1)** — one `<li>` per chapter where this entity is **named or referred to** (including dialogue about them, narration, or projected/remote scenes):

```html
<li data-chapter="0-prologue"><a href="../html/0-prologue.html#p-001">0-prologue p-001</a></li>
```

When the same entity is mentioned again in a later chapter scan, **append** a new `<li data-chapter="…">` to the existing `<ul class="chapter-refs">` (do not duplicate rows). Put `class="chapter-refs"` on the `<ul>`, not on the `<td>`. Only `<main data-chapters-scanned>` uses comma-separated chapter slugs; never put comma-separated slugs on `<ul>` or `<li>`.

**Wrong in Step 1.1:**

```html
<td><a href="../../prompts/james/james-base.html">James</a></td>
```

```html
<ul data-chapter="1-opportunity,2-resources">
```

```html
<td class="chapter-refs"><ul>...</ul></td>
```

## Merge rules

When scanning a chapter:

- **New entity** → add a row in the correct type table with Name (plain), Appearances (`<li>` for this chapter), Notes/Fixtures as needed.
- **Existing entity** (`id="entity-<slug>"` already present) → append a `<li data-chapter="…">` to **Appearances** if this chapter mentions them and that `data-chapter` is not already listed; optionally extend Notes or Fixtures.
- **Same name, different character** → do not auto-merge; use **open questions** or human review.
- **Deferred** — use only for entities the team explicitly parks (not merely “off-camera”); ordinary **mentions** still get an appearance `<li>` for this chapter.
- **Skipped** — real-world brands and proper nouns with no adaptation value unless visually depicted or story-relevant.
- **Deferred → created** — when a deferred entity is first mentioned in a chapter, add a **Created** row, append chapter `<li>` refs, and remove its bullet from **Deferred** (or note the promotion in **Open questions** if ambiguous).

Slug in `id` is the global contract: `prompts/<slug>/<slug>-base.html`.

On merge, preserve `<head>` and the stylesheet `<link>`; do not remove or inline CSS.

## Extraction rules

- Include only items that need a later prompt page: characters, scenes, portable props, and project-level styles.
- **Scenes** — one row per visitable environment (scene-heading granularity). Do not nest scenes as fixtures; do not use world/game titles as parent rows that swallow real scenes.
- **Fixtures** — set dressing inside a scene row for that scene only.
- **Props** — portable or reusable objects, not single-scene set dressing.
- **Characters** — name and variant notes only; no appearance, behavior, or plot in the manifest.
- **Appearances** — if the entity’s name or a clear reference appears anywhere in the chapter (including reported dialogue, flashbacks, projections, or “player on screen” feeds), add this chapter to **Appearances**. Step 1.2 will gather depiction detail from those paragraphs.

## Layout rules

- Single file: `story/adapted/entities.html`.
- Tables: **Characters**, **Scenes**, **Props**, **Art direction** (`data-type="style"`; not page CSS).
- Columns: **Name**, **Appearances**, **Notes** (and **Fixtures** on scene rows).
- No Slug column; slug is in `id="entity-<slug>"`.
- `<main class="entity-manifest" data-chapters-scanned="0-prologue,1-chapter-two">`.

## Manifest template

Use this skeleton when creating the file for the first time; preserve structure and the stylesheet link when merging.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Entity Manifest</title>
  <link rel="stylesheet" href="../../assets/screenplay.css">
</head>
<body>
  <main class="entity-manifest" data-chapters-scanned="" data-next-step="1.1" data-next-chapter="0-prologue">
    <h1>Entity Manifest</h1>
    <p class="pipeline-next" id="pipeline-next-1-1" data-step="1.1">Step 1.1 next: scan <strong>0-prologue</strong> — read <code>story/html/0-prologue.html</code></p>
    <p class="pipeline-next" id="pipeline-next-1-2" data-step="1.2">Step 1.2: waiting for manifest appearances</p>
    <section id="created">
      <h2>Created entities</h2>
      <section id="characters" class="entity-type" data-type="character">
        <h3>Characters</h3>
        <table class="entity-table">
          <thead><tr><th>Name</th><th>Appearances</th><th>Notes</th></tr></thead>
          <tbody>
            <tr id="entity-james">
              <td>James</td>
              <td>
                <ul class="chapter-refs">
                  <li data-chapter="0-prologue"><a href="../html/0-prologue.html#p-001">0-prologue p-001</a></li>
                </ul>
              </td>
              <td><ul class="notes"><li>Human and pony avatar are variants of one identity.</li></ul></td>
            </tr>
          </tbody>
        </table>
      </section>
      <!-- scenes, props, art direction (section id="styles" data-type="style") -->
    </section>
    <section id="deferred"><h2>Deferred entities</h2><ul class="deferred-list"></ul></section>
    <section id="skipped"><h2>Skipped proper nouns</h2><ul class="skipped-list"></ul></section>
    <section id="open-questions"><h2>Open questions</h2><ul></ul></section>
  </main>
</body>
</html>
```

## Coverage pass

Re-scan **this chapter’s** source HTML. Every candidate should land in a row, a fixture list, **deferred**, or **skipped**. Keep cells lightweight.

Validate: no `../../prompts/` in any **Name** cell; every mention in the chapter has an **Appearances** `<li>` with `data-chapter` matching the scanned slug (no duplicate `data-chapter` on the same row).

## Done when (single run)

`entities.html` was written on disk; this chapter’s slug is in `data-chapters-scanned`; every entity **mentioned** in the chapter has correct **Appearances** entries (including appended `<li>` on existing rows); pipeline-next lines reflect the next unit (1.1 chapter or first 1.2 slice).

## Done when (project)

All `story/html/*.html` chapters appear in `data-chapters-scanned`.

## User Prompt

The following user instructions (if provided) should help guide the above workflow.

**User:** `@$`

---
