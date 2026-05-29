---
name: 1-convert-originals
description: Convert user-provided Markdown originals into stable source HTML with paragraph anchors. Use when the user requests Step 1 or Markdown-to-HTML conversion for story originals.
---

# Step 1 — Convert originals

## Goal

Convert `story/original/<chapter>.md` into `story/html/<chapter>.html` without spending model tokens on prose conversion.

## Input

- `story/original/<chapter>.md`

## Output

- `story/html/<chapter>.html`

## Run

From the repository root:

```bash
.pi/skills/1-convert-originals/scripts/md-to-html story/original/0-prologue.md
```

The script may also be run for every Markdown file in `story/original/`:

```bash
.pi/skills/1-convert-originals/scripts/md-to-html
```

## HTML contract

Generated source HTML should be simple and deterministic:

```html
<article class="source-chapter" data-chapter="0-prologue">
  <h1 id="prologue-equestria-online">Prologue: Equestria Online</h1>
  <p id="p-001">James looked skeptically...</p>
</article>
```

- Paragraph IDs use `p-001`, `p-002`, and so on.
- Heading IDs are kebab-case slugs from heading text.
- The generated file includes a comment noting the source Markdown path.
- Do not add entity links in Step 1; entity links are introduced after the manifest exists.

## Done when

The HTML file exists under `story/html/`, preserves the source prose order, and contains stable paragraph anchors for later provenance links.
