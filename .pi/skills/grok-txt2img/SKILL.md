---
name: grok-txt2img
description: Turn a storyboard panel markdown file into a PNG via the Grok/xAI image API (txt2img). Use in Phase 6 when the user asks to generate a panel image.
---

# grok-txt2img

Run with the **bash** tool from the repository root:

```bash
.pi/skills/grok-txt2img/scripts/grok-txt2img storyboard/page-1/0-lab-wide.md
```

## Behavior

- Reads YAML frontmatter and uses the remaining file body as the image prompt.
- Uses `model`, `aspect_ratio` (or `size`), and `resolution` from frontmatter when present.
- Writes a timestamped PNG under `images/storyboard/`.
- Wiki links `[[prompts/...]]` in the body are passed through as-is (not expanded yet).

## Requirements

- `GROK_API_KEY` or `XAI_API_KEY` in a project-root `.env` file.
- Python 3 with the `requests` package installed.

The script prints a clear error and exits nonzero if the key is missing or the API call fails.
