---
name: 1-1-entity-manifest
description: Merge one chapter into the project entity manifest. Use for Step 1.1, entity listing, or continue to scan the next unscanned chapter.
---

# Step 1.1 — Entity manifest (project-wide)

## Goal

Maintain one project manifest by scanning **one chapter per run** and merging entities into it. Record display names, slugs, and chapter appearances. Do not create prompt pages or prompt links in Name cells—that is Step 1.2.

## Inputs

- `story/html/<chapter>.html` (the chapter to scan this run)
- `story/adapted/entities.html` (create from template if missing)

## Output

- Updated `story/adapted/entities.html`
- `data-chapters-scanned` on `<main>` appended with this chapter’s slug

**Retired:** do not create or update `story/adapted/<chapter>-entities.html`.

## Continue workflow (Step 1.1)

1. List chapter slugs that have `story/html/<chapter>.html`, in sorted order.
2. Read `data-chapters-scanned` on `<main class="entity-manifest">` (comma-separated slugs; empty if missing).
3. **Next chapter** = first HTML chapter **not** in `data-chapters-scanned`.
4. If the user names a chapter explicitly, scan that chapter instead (still merge into the project manifest).
5. Extract entities from that chapter only; **merge** into `entities.html` (see merge rules).
6. Append the chapter slug to `data-chapters-scanned`.
7. Process **one chapter**, then stop. Tell the user to say **continue** for Step 1.1 to scan the next chapter.

**Step 1.1 complete** when every `story/html/*.html` chapter is listed in `data-chapters-scanned`.

## Manifest link contract (shared with Step 1.2)

| Element | Step 1.1 | Step 1.2 |
|---------|----------|----------|
| **Name** | Plain text only | Link to `../../prompts/<slug>/<slug>-base.html` when that file is **first created** |
| **Chapters** (`ul.chapter-refs`) | Source links per chapter appearance | Mark slice done (see Step 1.2) |
| Row `id` | `entity-<slug>` | Unchanged |

**Chapters column (Step 1.1)** — one `<li>` per chapter where this entity appears:

```html
<li data-chapter="0-prologue"><a href="../html/0-prologue.html#p-001">0-prologue p-001</a></li>
```

When the same entity appears again in a later chapter scan, **append** a new `<li data-chapter="…">` (do not duplicate rows).

**Wrong in Step 1.1:**

```html
<td><a href="../../prompts/james/james-base.html">James</a></td>
```

## Merge rules

When scanning a chapter:

- **New entity** → add a row in the correct type table with Name (plain), Chapters (`<li>` for this chapter), Notes/Fixtures as needed.
- **Existing entity** (`id="entity-<slug>"` already present) → append a `<li data-chapter="…">` to **Chapters** only; optionally extend Notes or Fixtures if this chapter adds set dressing (keep brief).
- **Same name, different character** → do not auto-merge; use **open questions** or human review.
- **Deferred / skipped** → project-level lists; append chapter context in the bullet when first mentioned in that chapter.

Slug in `id` is the global contract: `prompts/<slug>/<slug>-base.html`.

## Extraction rules

- Include only items that need a later prompt page: characters, scenes, portable props, and project-level styles.
- **Scenes** — one row per visitable environment (scene-heading granularity). Do not nest scenes as fixtures; do not use world/game titles as parent rows that swallow real scenes.
- **Fixtures** — set dressing inside a scene row for that scene only.
- **Props** — portable or reusable objects, not single-scene set dressing.
- **Characters** — name and variant notes only; no appearance, behavior, or plot in the manifest.
- Defer entities mentioned but not shown on screen; skip real-world brands unless visually depicted in the chapter.

## Layout rules

- Single file: `story/adapted/entities.html`.
- Tables: **Characters**, **Scenes**, **Props**, **Styles** (if needed).
- Columns: **Name**, **Chapters**, **Notes** (and **Fixtures** on scene rows).
- No Slug column; slug is in `id="entity-<slug>"`.
- `<main class="entity-manifest" data-chapters-scanned="0-prologue,1-chapter-two">`.

## Manifest template

Use this skeleton when creating the file for the first time; preserve styles and structure when merging.

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>Entity Manifest</title>
  <!-- shared stylesheet: see story/adapted/entities.html -->
</head>
<body>
  <main class="entity-manifest" data-chapters-scanned="">
    <h1>Entity Manifest</h1>
    <p class="meta">Project-wide catalog. Step 1.1 merges one chapter per run.</p>
    <section id="created">
      <h2>Created entities</h2>
      <section id="characters" class="entity-type" data-type="character">
        <h3>Characters</h3>
        <table class="entity-table">
          <thead><tr><th>Name</th><th>Chapters</th><th>Notes</th></tr></thead>
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
      <!-- scenes, props, styles -->
    </section>
    <section id="deferred"><h2>Deferred entities</h2><ul></ul></section>
    <section id="skipped"><h2>Skipped proper nouns</h2><ul></ul></section>
    <section id="open-questions"><h2>Open questions</h2><ul></ul></section>
  </main>
</body>
</html>
```

Copy full CSS from the existing `story/adapted/entities.html` when bootstrapping.

## Coverage pass

Re-scan **this chapter’s** source HTML. Every candidate should land in a row, a fixture list, **deferred**, or **skipped**. Keep cells lightweight.

Validate: no `../../prompts/` in any **Name** cell; every new appearance has a **Chapters** `<li>` with `data-chapter` matching the scanned slug.

## Done when (single run)

This chapter’s slug is in `data-chapters-scanned`, and every entity found in the chapter is merged into `entities.html` with correct **Chapters** entries.

## Done when (project)

All `story/html/*.html` chapters appear in `data-chapters-scanned`.

## User Prompt

**User:** `@$`

---
