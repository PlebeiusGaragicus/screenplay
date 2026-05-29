---
name: 1.2-entity-prompts
description: Create entity prompt HTML pages from an approved chapter entity manifest. Use when the user requests Step 1.2 or entity prompt page creation.
---

# Step 1.2 — Entity prompt pages

## Goal

Create one grounded HTML prompt page per approved manifest item, with source quotes and links back to chapter paragraphs. Step 1.2 is where descriptions are actually extracted and written.

## Inputs

- `story/html/<chapter>.html`
- `story/adapted/<chapter>-entities.html`
- Optional: `story/adapted/<chapter>-screenplay.html`

## Output

- `prompts/<entity>/<entity>-base.html`

Use `-base.html` for the first stable version. Add another version only when the user explicitly wants a separate prompt variant.

## Rules

- Iterate through manifest rows one item at a time. The next item is the first manifest name link whose target prompt file does not exist yet.
- The presence of the linked prompt file means that item is complete; after creating the page, ensure the manifest link points to the finished file.
- Ground every description in source evidence linked to paragraph anchors.
- Keep inference separate from direct evidence.
- Record variants (alternate forms, costumes, states, ages, etc.) in a visible section; do not overwrite the base identity silently.
- Link back to the manifest row and source paragraphs.
- Use the manifest's first appearance / mention as the starting point, then search the source chapter for all paragraphs that materially describe or clarify that item.
- For **characters**, emphasize appearance, mood, behavior, voice, posture, and recurring mannerisms. Do not turn the page into a plot summary.
- For **scenes/locations**, describe layout, atmosphere, lighting, materials, and fixtures listed in the manifest. Fixtures remain part of the scene page unless the manifest explicitly promotes them to props.
- For **props**, describe portable or reusable objects that need independent prompting across scenes.

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
        <dd><a href="../../story/adapted/0-prologue-entities.html#entity-james">0-prologue entity manifest</a></dd>
      </dl>
    </header>

    <section class="evidence">
      <h2>Evidence</h2>
      <blockquote cite="../../story/html/0-prologue.html#p-001">
        <p>Brief source quote.</p>
        <footer><a href="../../story/html/0-prologue.html#p-001">0-prologue p-001</a></footer>
      </blockquote>
    </section>

    <section class="description">
      <h2>Description</h2>
      <p>Grounded visual and story-relevant description.</p>
    </section>

    <section class="fixtures">
      <h2>Fixtures</h2>
      <p>Use this section for scene/location pages only. List fixed scene elements such as furniture, architecture, workstations, lighting sources, and local set dressing.</p>
    </section>

    <section class="variants">
      <h2>Variants</h2>
      <ul>
        <li><strong>Base:</strong> Default appearance and identity.</li>
      </ul>
    </section>

    <section class="inferences">
      <h2>Inferences</h2>
      <p>Clearly label any visual detail inferred from context rather than stated directly.</p>
    </section>
  </article>
</body>
</html>
```

## Path convention

From `prompts/<entity>/<entity>-base.html`:

- Source chapter links usually look like `../../story/html/<chapter>.html#p-001`.
- Manifest links usually look like `../../story/adapted/<chapter>-entities.html#entity-<slug>`.
- Related prompt page links usually look like `../<other-entity>/<other-entity>-base.html`.

## Iteration checklist

For the next unfinished manifest row:

1. Follow the linked prompt-page path from the manifest name cell.
2. If the target file already exists, skip it and move to the next manifest row.
3. Start from the first appearance / mention paragraph.
4. Search the source chapter for additional relevant paragraphs.
5. Write the prompt page with evidence quotes, grounded description, variants, fixtures, and clearly labeled inferences.
6. Verify the manifest name link points to the prompt page you created.
7. Stop unless the user explicitly asked you to process multiple items in one run.

## Done when

Every manifest row has a corresponding prompt page, each page links back to its manifest row plus at least one source paragraph, and scene pages incorporate their manifest fixtures rather than spawning unnecessary prop pages.
