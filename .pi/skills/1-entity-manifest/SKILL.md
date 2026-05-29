---
name: 1-entity-manifest
description: Create a chapter-level HTML named entity manifest from source HTML. Use when the user requests Step 1.1, entity listing, or entity manifest creation for a chapter.
---

# Step 1.1 — Entity manifest

## Goal

Create a chapter-level named entity manifest before writing entity prompt pages. This fixes slug, merge, defer, and skip decisions early.

## Inputs

- `story/html/<chapter>.html`

## Output

- `story/adapted/<chapter>-entities.html`

## Extraction rules

- Include named characters, recurring visual locations, visually important props, and project-level styles established by the chapter.
- Defer entities mentioned but not yet visually described when the user should decide later.
- Skip real-world brands, games, companies, or abstract concepts unless they are visually depicted in the chapter.
- Treat alternate forms as variants of one identity when appropriate (for example, a human and their in-game avatar).
- Prefer conservative slugs; once approved, slugs become cross-file contract.

## Layout rules

- **Do not** use one flat table for all entity types.
- Group **create** rows into separate tables under:
  - `Characters`
  - `Locations`
  - `Props`
  - `Styles` (only when needed)
- Omit a type section if the chapter has no entities of that type.
- Use the shared manifest stylesheet in the template (borders, zebra rows, bullet lists).
- **Evidence** must be a `<ul class="evidence">` with one `<li>` per source paragraph (link + short quote).
- **Notes** may be a short paragraph or a `<ul class="notes">` when there are multiple points.
- Row `id` must match the slug in the Slug column: `id="entity-<slug>"` (use the `<code>` slug, not an abbreviated id).
- Do not include a Type column; the section heading is the type.

## Manifest template

```html
<!doctype html>
<html lang="en">
<head>
  <meta charset="utf-8">
  <title>0-prologue Entity Manifest</title>
  <style>
    :root {
      --text: #1c1c1c;
      --muted: #5c5c5c;
      --border: #c4b8a8;
      --border-strong: #9a8b78;
      --zebra: #faf8f5;
      --head: #ebe4d9;
    }
    body {
      font-family: "Segoe UI", system-ui, sans-serif;
      line-height: 1.5;
      color: var(--text);
      max-width: 52rem;
      margin: 1.5rem auto;
      padding: 0 1rem 2rem;
    }
    h1 { margin-top: 0; }
    h2 {
      font-size: 1.15rem;
      margin: 1.5rem 0 0.5rem;
      border-bottom: 2px solid var(--border-strong);
      padding-bottom: 0.25rem;
    }
    h3 {
      font-size: 1rem;
      margin: 1rem 0 0.4rem;
      color: var(--text);
    }
    a { color: #1d4ed8; }
    table.entity-table {
      width: 100%;
      border-collapse: collapse;
      font-size: 0.92rem;
      border: 2px solid var(--border-strong);
      margin-bottom: 0.5rem;
    }
    table.entity-table th,
    table.entity-table td {
      border: 1px solid var(--border);
      padding: 0.5rem 0.65rem;
      vertical-align: top;
      text-align: left;
    }
    table.entity-table th {
      background: var(--head);
      border-bottom: 2px solid var(--border-strong);
    }
    table.entity-table tbody tr:nth-child(even) { background: var(--zebra); }
    ul.evidence, ul.notes, section#deferred ul, section#skipped ul, section#open-questions ul {
      margin: 0.25rem 0;
      padding-left: 1.25rem;
    }
    ul.evidence li, ul.notes li { margin: 0.2rem 0; }
    section#deferred ul li, section#skipped ul li, section#open-questions ul li {
      margin: 0.35rem 0;
      padding-bottom: 0.35rem;
      border-bottom: 1px dotted var(--border);
    }
    section#deferred ul li:last-child,
    section#skipped ul li:last-child,
    section#open-questions ul li:last-child { border-bottom: none; }
    code { background: #f0ebe3; padding: 0.1em 0.35em; border-radius: 3px; }
  </style>
</head>
<body>
  <main class="entity-manifest" data-chapter="0-prologue">
    <h1>0-prologue Entity Manifest</h1>
    <p>Source: <a href="../html/0-prologue.html">story/html/0-prologue.html</a></p>

    <section id="created">
      <h2>Created entities</h2>

      <section id="characters" class="entity-type" data-type="character">
        <h3>Characters</h3>
        <table class="entity-table">
          <thead>
            <tr>
              <th>Name</th>
              <th>Slug</th>
              <th>Action</th>
              <th>Evidence</th>
              <th>Notes</th>
            </tr>
          </thead>
          <tbody>
            <tr id="entity-james">
              <td>James</td>
              <td><code>james</code></td>
              <td>create</td>
              <td>
                <ul class="evidence">
                  <li><a href="../html/0-prologue.html#p-001">p-001</a>: opens the chapter</li>
                </ul>
              </td>
              <td>
                <ul class="notes">
                  <li>Human and pony avatar are variants of one identity.</li>
                </ul>
              </td>
            </tr>
          </tbody>
        </table>
      </section>

      <section id="locations" class="entity-type" data-type="location">
        <h3>Locations</h3>
        <table class="entity-table">
          <thead>
            <tr>
              <th>Name</th>
              <th>Slug</th>
              <th>Action</th>
              <th>Evidence</th>
              <th>Notes</th>
            </tr>
          </thead>
          <tbody>
            <tr id="entity-equestria-online">
              <td>Equestria Online</td>
              <td><code>equestria-online</code></td>
              <td>create</td>
              <td>
                <ul class="evidence">
                  <li><a href="../html/0-prologue.html#p-002">p-002</a>: the official My Little Pony MMO</li>
                </ul>
              </td>
              <td>The game world; overarching setting for in-game scenes.</td>
            </tr>
          </tbody>
        </table>
      </section>

      <section id="props" class="entity-type" data-type="prop">
        <h3>Props</h3>
        <table class="entity-table">
          <thead>
            <tr>
              <th>Name</th>
              <th>Slug</th>
              <th>Action</th>
              <th>Evidence</th>
              <th>Notes</th>
            </tr>
          </thead>
          <tbody>
            <tr id="entity-webcam">
              <td>Embedded Webcam</td>
              <td><code>webcam</code></td>
              <td>create</td>
              <td>
                <ul class="evidence">
                  <li><a href="../html/0-prologue.html#p-014">p-014</a>: face mapping via embedded webcam</li>
                </ul>
              </td>
              <td>Key visual mechanic for avatar mimicry.</td>
            </tr>
          </tbody>
        </table>
      </section>
    </section>

    <section id="deferred">
      <h2>Deferred entities</h2>
      <ul>
        <li><strong>Canterlot</strong> — mentioned, not visually depicted in this chapter.</li>
      </ul>
    </section>

    <section id="skipped">
      <h2>Skipped proper nouns</h2>
      <ul>
        <li><strong>Hasbro</strong> — real-world brand. Skipped per rules.</li>
      </ul>
    </section>

    <section id="open-questions">
      <h2>Open questions</h2>
      <ul>
        <li>Should the real-world internet cafe be tracked if it reappears?</li>
      </ul>
    </section>
  </main>
</body>
</html>
```

## Coverage pass

Before finishing, re-scan the source chapter for proper nouns, titles, repeated settings, and recurring visual objects. Every candidate should appear under **create** (in the correct type table), **deferred**, or **skipped**.

## Human checkpoint

Recommend human review after this step, especially on the first chapter. Do not proceed to entity prompt pages if slug or granularity questions remain unresolved.

## Done when

The manifest exists, source links point to `story/html/<chapter>.html#p-xxx`, each **create** row sits in the correct type table with bulleted evidence, and deferred/skipped/open questions use bullet lists.

## User Prompt

Use the following user prompt (if provided) for specific instructions regarding the above plan

**User:**
`@$`

---
