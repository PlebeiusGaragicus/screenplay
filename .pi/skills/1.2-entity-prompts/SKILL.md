---
name: 1.2-entity-prompts
description: Build entity prompt pages from the project manifest, one entity×chapter slice per run. Use for Step 1.2 or continue for the next slice.
---

# Step 1.2 — Entity prompt pages (project-wide)

## Goal

One HTML prompt page per entity (`prompts/<slug>/<slug>-base.html`), with **one section per chapter** for evidence (and chapter-scoped description where useful). Each run processes exactly **one (entity, chapter) slice**: extract visual descriptions, behaviors, moods, costumes, voice, atmosphere, and variants from that chapter’s source HTML only.

## Inputs

- `story/adapted/entities.html`
- `story/html/<chapter>.html` for the slice’s chapter
- Optional: `story/adapted/<chapter>-screenplay.html`

## Output

- Created or updated `prompts/<slug>/<slug>-base.html`
- Updated manifest: **Name** link (when file is first created) and **Chapters** `<li>` marked slice-done for this chapter

**Retired:** do not read or write `story/adapted/<chapter>-entities.html`.

## Manifest link contract (shared with Step 1.1)

| Signal | Meaning |
|--------|---------|
| Plain **Name** | No `prompts/<slug>/<slug>-base.html` yet |
| Linked **Name** | Prompt file exists (entity “opened”) — link as soon as the file is created, not when all slices are done |
| `<li data-chapter="…">` without `data-slice-done` | Slice pending |
| `<li data-chapter="…" data-slice-done="true">` | Slice complete |

**Pending chapter ref (Step 1.1):**

```html
<li data-chapter="0-prologue"><a href="../html/0-prologue.html#p-001">0-prologue p-001</a></li>
```

**Slice done (Step 1.2)** — primary link points at the evidence section on the prompt page; keep source paragraph in parentheses:

```html
<li data-chapter="0-prologue" data-slice-done="true">
  <a href="../../prompts/james/james-base.html#evidence-0-prologue">0-prologue</a>
  (<a href="../html/0-prologue.html#p-001">p-001</a>)
</li>
```

## Continue workflow (Step 1.2)

1. Open `story/adapted/entities.html`.
2. Walk **Created entities** tables in order: **Characters** → **Scenes** → **Props** → **Styles**.
3. For each row, walk `<ul class="chapter-refs">` items in chapter slug order (`data-chapter`).
4. **Next slice** = first `<li>` **without** `data-slice-done="true"` whose evidence section is missing or empty in the prompt file (`#evidence-<chapter>` or `section.evidence[data-chapter="<chapter>"]`).
5. Read slug from `id="entity-<slug>"`; read chapter from `data-chapter` on that `<li>`.
6. Process **one** slice, then stop. Tell the user to say **continue** for the next slice.

Optional: user may restrict to one entity (`continue 1.2 for james`) — only consider `<li>` elements on that row.

**Step 1.2 complete** when every `<li class="chapter-refs">` (across all rows) has `data-slice-done="true"` and a non-empty evidence section on the prompt page.

If **continue** finds no pending slices, report Step 1.2 complete for the project.

## Per-slice workflow

1. Resolve entity slug and chapter slug from the manifest row.
2. Read `story/html/<chapter>.html` (full chapter; focus on paragraphs relevant to this entity).
3. If `prompts/<slug>/<slug>-base.html` does not exist, create it from the template (header, empty variants/inferences as needed).
4. Add or replace `section.evidence[data-chapter="<chapter>"]` with `id="evidence-<chapter>"` and ordered blockquotes.
5. Add or update `section.description[data-chapter="<chapter>"]` with a polished summary for **this chapter’s** material (project-wide description can synthesize later slices in place or in the same section—prefer one description section per chapter when the entity spans multiple chapters).
6. Update **Variants** / **Fixtures** / **Inferences** when this chapter adds new forms or set dressing.
7. If the file was just created, set **Name** to a link in the manifest.
8. Set the chapter `<li>` to `data-slice-done="true"` with links as in the contract above.

Do not batch multiple slices unless the user explicitly asks.

## Source extraction (this chapter only)

- Ground claims in **Evidence** blockquotes with links to `story/html/<chapter>.html#p-xxx`.
- Order blockquotes by paragraph id within the chapter section.
- Keep **Inferences** separate from stated facts.
- No plot summaries—only depiction-relevant detail.

### By entity type

**Characters** — appearance, costumes/alternate forms, mood, behavior, posture, voice, mannerisms.

**Scenes** — layout, atmosphere, lighting, materials; expand manifest **Fixtures** for this chapter.

**Props** — form, materials, scale, handling.

**Styles** — art direction when the row is `data-type="style"`.

## Entity page template

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>James — Base Prompt</title>
</head>
<body>
  <article class="entity-prompt" data-entity="james" data-type="character" data-version="base">
    <header>
      <h1>James</h1>
      <dl>
        <dt>Slug</dt>
        <dd><code>james</code></dd>
        <dt>Type</dt>
        <dd>character</dd>
        <dt>Manifest</dt>
        <dd><a href="../../story/adapted/entities.html#entity-james">Entity manifest</a></dd>
      </dl>
    </header>

    <section class="evidence" data-chapter="0-prologue" id="evidence-0-prologue">
      <h2>Evidence — 0-prologue</h2>
      <blockquote cite="../../story/html/0-prologue.html#p-001">
        <p>Brief quote.</p>
        <footer><a href="../../story/html/0-prologue.html#p-001">0-prologue p-001</a></footer>
      </blockquote>
    </section>

    <section class="description" data-chapter="0-prologue">
      <h2>Description — 0-prologue</h2>
      <p>Chapter-scoped illustration-ready summary.</p>
    </section>

    <section class="fixtures">
      <h2>Fixtures</h2>
      <p>Scene entities: chapter-specific fixture detail when applicable.</p>
    </section>

    <section class="variants">
      <h2>Variants</h2>
      <ul><li><strong>Human:</strong> …</li></ul>
    </section>

    <section class="inferences">
      <h2>Inferences</h2>
      <p>Labeled inferences only.</p>
    </section>
  </article>
</body>
</html>
```

Additional chapters append new `section.evidence` / `section.description` blocks with matching `data-chapter` and `id="evidence-<chapter>"`.

## Path convention

From `prompts/<slug>/<slug>-base.html`:

- Source: `../../story/html/<chapter>.html#p-xxx`
- Manifest: `../../story/adapted/entities.html#entity-<slug>`
- Other entities (if linked): `../<other-slug>/<other-slug>-base.html`

## Per-run checklist

1. Find next pending slice in `entities.html`.
2. Write or update evidence (and description) for that chapter on the prompt page.
3. Link **Name** if the file was just created.
4. Mark the chapter `<li>` with `data-slice-done="true"`.
5. Stop; suggest **continue** for the next slice.

## Done when (project)

Every `chapter-refs` item has `data-slice-done="true"`, each linked **Name** resolves to an existing prompt file, and each slice has a non-empty `#evidence-<chapter>` section.

## User Prompt

**User:** `@$`

---
