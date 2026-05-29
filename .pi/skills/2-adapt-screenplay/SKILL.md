---
name: 2-adapt-screenplay
description: Adapt one chapter to screenplay HTML. Step 2; bare continue uses this after Step 1.2 is complete, one chapter per run.
---

# Step 2 — Adapt screenplay

## Bare continue

When the user says **`continue`** and AGENTS.md routes here (Steps 1.1 and 1.2 complete for the project):

1. List `story/html/*.html` chapter slugs in order; pick the first slug with no `story/adapted/<chapter>-screenplay.html`.
2. Read **only** that chapter’s source HTML and `entities.html` (for links—not full prompt corpora).
3. Write `story/adapted/<chapter>-screenplay.html`, then **stop**.

## Persist artifacts (mandatory)

- You **must** `write` or `edit` the screenplay file before ending the run.
- **Re-read** the file to confirm it exists on disk before claiming done.

## Goal

Adapt a source chapter into navigable, semantic screenplay HTML linked to the entity manifest and expected prompt pages.

## Inputs

- `story/html/<chapter>.html`
- `story/adapted/entities.html` (project manifest; entities that appear in this chapter)

## Output

- `story/adapted/<chapter>-screenplay.html`

## Rules

- Use semantic HTML classes rather than Markdown or wiki links.
- Scene headings use all caps in `<h2 class="scene-heading">`.
- Preserve the source's intent and key wording while adapting prose into action and dialogue.
- Link visible entity mentions to their expected prompt pages, using the slug from the manifest.
- Prompt pages are created in Step 1.2; link visible entity mentions to those pages when available.
- Link back to source paragraphs where helpful via `story/html/<chapter>.html#p-xxx`.
- Do not introduce entities that are not represented in the manifest unless the user approves updating Step 1.1/1.2 artifacts first.

## Screenplay template

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>0-prologue Screenplay</title>
  <link rel="stylesheet" href="../../assets/screenplay.css">
</head>
<body>
  <main class="screenplay" data-chapter="0-prologue">
    <h1>0-prologue Screenplay</h1>
    <p>Source: <a href="../html/0-prologue.html">story/html/0-prologue.html</a></p>
    <p>Entities: <a href="entities.html">story/adapted/entities.html</a></p>

    <section class="scene" id="scene-computer-lab-night">
      <h2 class="scene-heading">INT. COMPUTER LAB - NIGHT</h2>
      <p class="action">
        <a href="../../prompts/james/james-base.html">James</a> sits at a terminal.
      </p>
      <p class="character">JAMES</p>
      <p class="dialogue">I'm still not sure I want to play something so pink and purple.</p>
    </section>
  </main>
</body>
</html>
```

## Done when

The screenplay HTML covers the agreed chapter scope, uses semantic screenplay classes, and all internal links point to source HTML, the manifest, or expected prompt page paths.

## User Input

The following user instructions (if provided) should help guide the above workflow.

**User:** `$@`

---