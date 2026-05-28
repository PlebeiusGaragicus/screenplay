---
name: grok-imagen
description: Reads a storyboard panel markdown file, extracts the prompt and generation parameters, then calls the Grok image generation API (txt2img). Use when you need to turn a panel description into an image.
---

# Grok Imagen

## Usage
```bash
/skill:grok-imagen storyboard/panel-01.md
```

- Loads the panel file
- Reads `model`, `aspect_ratio` (or `size`), `resolution` from YAML frontmatter (with sensible defaults)
- Uses the remaining content as the image prompt
- Calls the Grok API via `scripts/generate_image.py` (Python) and saves a timestamped PNG

The user manages the `GROK_API_KEY` (or `XAI_API_KEY`) in `.env`. The script will error with instructions if the key is missing.
