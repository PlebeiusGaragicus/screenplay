---
name: 1-2-entity-prompts
description: Build entity prompt pages from the manifest. Step 1.2; bare continue uses this after Step 1.1 is complete, one entity×chapter slice per run.
---

# Step 1.2 — Entity prompt pages (project-wide)

## Bare continue

When the user says **`continue`** and AGENTS.md routes here (Step 1.1 complete, pending appearance slices):

1. Open `story/adapted/entities.html` and read `#pipeline-next-1-2` / `data-next-entity` / `data-next-chapter`.
2. Read **only** `story/html/<that-chapter>.html`.
3. Update `prompts/<slug>/<slug>-base.html` and the manifest slice for that entity×chapter, then **stop**.

Do not scan an unscanned manifest chapter (that is Step 1.1).

## Persist artifacts (mandatory)

- You **must** `write` or `edit` both `prompts/<slug>/<slug>-base.html` (if needed) **and** `story/adapted/entities.html`.
- **Re-read** both files and confirm the manifest `<li>` for this chapter has `data-slice-done="true"`.
- Do not report a slice as done unless those attributes are present on disk.

## Goal

One HTML prompt page per entity (`prompts/<slug>/<slug>-base.html`), with **one section per chapter** for evidence (and chapter-scoped description where useful). Each run processes exactly **one (entity, chapter) slice**: extract visual descriptions, behaviors, moods, costumes, voice, atmosphere, and variants from that chapter’s source HTML only.

## Inputs

- `story/adapted/entities.html` (read pipeline-next and/or walk appearances to choose the slice)
- `story/html/<chapter>.html` for the slice’s chapter — **only this chapter** in context for extraction
- Optional: `story/adapted/<chapter>-screenplay.html`

## Output

- Created or updated `prompts/<slug>/<slug>-base.html` (including `section.appearances` in sync)
- Updated manifest: **Name** link (when file is first created), **Appearances** `<li>` marked slice-done, pipeline-next on manifest

**Retired:** do not read or write `story/adapted/<chapter>-entities.html`.

## Manifest link contract (shared with Step 1.1)

| Signal | Meaning |
|--------|---------|
| Plain **Name** | No `prompts/<slug>/<slug>-base.html` yet |
| Linked **Name** | Prompt file exists (entity “opened”) — link as soon as the file is created, not when all slices are done |
| `<li data-chapter="…">` without `data-slice-done` | Slice pending |
| `<li data-chapter="…" data-slice-done="true">` | Slice complete |

**Pending appearance (Step 1.1):**

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

## Pipeline next (update every run)

Before stopping, update on `entities.html`:

- `#pipeline-next-1-2` — e.g. `Step 1.2 next: <strong>honeycrisp</strong> — <strong>0-prologue</strong> — read <code>story/html/0-prologue.html</code>`
- `<main>` attributes: `data-next-step="1.2"`, `data-next-entity="<slug>"`, `data-next-chapter="<chapter>"` for the **following** pending slice (after the one you just finished)

When no pending slices remain: `#pipeline-next-1-2` → `Step 1.2: complete`; clear `data-next-entity` and `data-next-chapter`.

Agents may read `#pipeline-next-1-2` and `data-next-*` first instead of re-walking the whole manifest.

## Continue workflow (Step 1.2)

1. Open `story/adapted/entities.html` and read `#pipeline-next-1-2` / `data-next-entity` / `data-next-chapter`, **or** walk tables as below.
2. Walk **Created entities** tables in order: **Characters** → **Scenes** → **Props** → **Styles**.
3. For each row, walk `<ul class="chapter-refs">` items in chapter slug order (`data-chapter`).
4. **Next slice** = first `<li>` **without** `data-slice-done="true"`.
5. Read slug from `id="entity-<slug>"`; read chapter from `data-chapter` on that `<li>`.
6. Process **one** slice, then stop. Tell the user to say **continue** for the next slice.

Optional: user may restrict to one entity (`continue 1.2 for james`) — only consider `<li>` elements on that row.

**Step 1.2 complete** when every appearance `<li>` has `data-slice-done="true"`, each prompt page’s `section.appearances` matches, and each slice has a non-empty `#evidence-<chapter>` section.

If **continue** finds no pending slices, report Step 1.2 complete for the project.

## Per-slice workflow

1. Resolve entity slug and chapter slug from the manifest row (the pending `<li>`).
2. Read `story/html/<chapter>.html` only (focus on paragraphs relevant to this entity).
3. If `prompts/<slug>/<slug>-base.html` does not exist, create it from the template (`section.appearances` first, then header).
4. Add or replace `section.evidence[data-chapter="<chapter>"]` with `id="evidence-<chapter>"` and ordered blockquotes.
5. Add or update `section.description[data-chapter="<chapter>"]` with a polished summary for **this chapter’s** material.
6. Update **Variants** / **Fixtures** / **Inferences** when this chapter adds new forms or set dressing.
7. Update **`section.appearances`** — one `<li data-chapter="…">` per manifest appearance; set `data-slice-done="true"` on the slice you just finished (mirror manifest).
8. If the file was just created, set **Name** to a link in the manifest.
9. Set the manifest appearance `<li>` to `data-slice-done="true"` with links as in the contract above.
10. Update pipeline-next on the manifest for the **next** pending slice.

**Mandatory:** never end a 1.2 run without steps 7, 9, and 10. If evidence already existed from a prior partial run, this run still marks the slice done and syncs appearances.

Do not batch multiple slices unless the user explicitly asks.

## Source extraction (this chapter only)

- Ground claims in **Evidence** blockquotes with links to `story/html/<chapter>.html#p-xxx`.
- Order blockquotes by paragraph id within the chapter section.
- Keep **Inferences** separate from stated facts.
- No plot summaries—only depiction-relevant detail.
- Include paragraphs where the entity is **mentioned** even if not physically “on screen” in the scene (e.g. dialogue about them, feeds, projections).

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
  <link rel="stylesheet" href="../../assets/screenplay.css">
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

    <section class="appearances" aria-label="Appearances">
      <h2>Appearances</h2>
      <ul class="appearances-list">
        <li data-chapter="0-prologue" data-slice-done="true">
          <a href="#evidence-0-prologue">0-prologue</a>
        </li>
      </ul>
    </section>

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

Pending appearance on the entity page (no `data-slice-done` yet):

```html
<li data-chapter="2-resources">
  <a href="../../story/html/2-resources.html">2-resources</a>
</li>
```

Additional chapters append evidence/description blocks and new `<li>` entries in `section.appearances`.

## Path convention

From `prompts/<slug>/<slug>-base.html`:

- Source: `../../story/html/<chapter>.html#p-xxx`
- Manifest: `../../story/adapted/entities.html#entity-<slug>`
- Other entities (if linked): `../<other-slug>/<other-slug>-base.html`

## Per-run checklist

1. Resolve next pending slice (`#pipeline-next-1-2` or manifest walk).
2. Read only that chapter’s source HTML; write or update evidence and description.
3. Sync `section.appearances` on the prompt page.
4. Link **Name** in the manifest if the file was just created.
5. Mark manifest `<li>` with `data-slice-done="true"` (`write`/`edit`, then verify).
6. Update `#pipeline-next-1-2` and `data-next-*` for the **following** slice.
7. Stop; suggest **continue** for the next unit.

## Done when (project)

Every appearance `<li>` has `data-slice-done="true"`, each prompt page’s appearances list matches the manifest, each linked **Name** resolves to an existing prompt file, and each slice has a non-empty `#evidence-<chapter>` section.

## User Prompt

The following user instructions (if provided) should help guide the above workflow.

**User:** `@$`

---
