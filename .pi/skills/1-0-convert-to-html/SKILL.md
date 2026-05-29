---
name: 1-0-convert-to-html
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
.pi/skills/1-0-convert-to-html/scripts/md-to-html story/original/0-prologue.md
```

The script may also be run for every Markdown file in `story/original/`:

```bash
.pi/skills/1-0-convert-to-html/scripts/md-to-html
```

## After converting

- **Do not** read, summarize, or review the generated HTML in the same run. Conversion is complete once the script exits successfully.
- The user opens and checks `story/html/<chapter>.html` in a browser (or via `./read`) before starting Step 1.1.

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
- `<head>` must include `<link rel="stylesheet" href="../../assets/screenplay.css">` (the conversion script adds this automatically).
- Do not add entity links in Step 1; entity links are introduced after the manifest exists.

## Done when

The HTML file exists under `story/html/`, preserves the source prose order, and contains stable paragraph anchors for later provenance links. Stop there—do not continue into later pipeline steps unless the user asks.

## User Prompt

The following user instructions (if provided) should help guide the above workflow.

**User:** `@$`

---
