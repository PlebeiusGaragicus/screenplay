---
name: 2-adapt-screenplay
description: Adapt source HTML into semantic Fountain-style screenplay HTML. Use when the user requests Step 2 or screenplay adaptation for a chapter.
---

# Step 2 — Adapt screenplay

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

The following user input (if provided) should guide the above process.

**User:** `$@`

---