# AGENTS.md - Comic/Video Generation Project

## Overview

This repository is a template for creating AI-generated comic books and/or videos. It uses a custom Fountain-flavored markdown format with wiki-style links to connect screenplays in `./story/` with visual prompts (characters, locations, etc.) in `./prompts/`. 

All content files utilize YAML frontmatter.

## Roles
- **Human**: Maintains high-level documentation and general oversight.
- **Agent**: Manages and creates all other repository content (story, prompts) and updates this file (`AGENTS.md`).

## Core Rules
- All story/*.md and prompts/*.md files use Fountain format + `[[wiki-style links]]`.
- Include YAML frontmatter at the top of every content file.
- Story files live in ./story/; reusable prompts/descriptions in ./prompts/ (with subdirs for characters/, locations/, styles/, etc.).
- Generated/reference images go in ./images/.

# Workflow — Screenplay / Comic / Video Pipeline

We outline the end-to-end process for turning source material into AI-generated comics or videos using the repo conventions.

## High-Level Stages

1. **Ingest Original Story**
   - Place raw/clean source in `story/origional/*.md` (plain text or lightly formatted).
   - Keep filenames stable (e.g., `0-prologue.md`).

2. **Extract Reusable Prompts**
   - Pull characters, locations, styles, etc. into `prompts/{characters,locations,styles}/*.md`.
   - Each file gets YAML frontmatter (`name`, `type`, description).
   - Use consistent kebab-case filenames and Title Case keys.
   - Update `screenplay.json` "prompts" map + top-level arrays.
   - Use `[[wiki-style links]]` everywhere.

3. **Adapt into Screenplay**
   - Create Fountain-flavored versions in `story/adapted/*.md`.
   - Include YAML frontmatter listing characters/locations/prompt paths.
   - Embed `[[prompts/...]]` links for every reusable element.
   - Maintain scene headings, action, dialogue, etc.

4. **Generate Reference Images (Iterative)**
   - Use the descriptions in `prompts/*.md` as base prompts for your image model (Imagen, Midjourney, Flux, etc.).
   - Iterate until characters/locations look consistent (face, colors, style, lighting).
   - Store final reference images in `images/` (e.g., `images/characters/james-ref.png`).
   - Optionally add a `references` field or note in the prompt `.md` files or `screenplay.json`.
   - This stage can take many prompt tweaks—keep the `.md` files as the single source of truth.

5. **Build Storyboard**
   - Create `storyboard/*.md` files (one per key scene or panel).
   - Each storyboard file is a focused image/video generation prompt that **combines**:
     - Character refs (`[[prompts/characters/james.md]]`)
     - Location refs (`[[prompts/locations/barn-interior.md]]`)
     - Style (`[[prompts/styles/equestria-online.md]]`)
     - Specific action, camera angle, lighting, composition from the adapted screenplay.
   - Example structure:
     ```
     ---
     scene: "INT. BARN - Honeycrisp greeting"
     panel: 1
     ---
     [[prompts/characters/james.md]] [[prompts/locations/barn-interior.md]] ...
     Detailed action description + camera notes.
     ```
   - These become the direct prompts for comic panels or video shots.

6. **Generate & Assemble**
   - Feed storyboard prompts → image/video model.
   - Place outputs in `images/storyboard/` or similar.
   - Update `screenplay.json` with any new prompt paths or version bump.
   - (Future) Add comic layout or video editing scripts as needed.

## Naming & Conventions
- kebab-case files, Title Case keys.
- Always YAML frontmatter on content files.
- Keep `screenplay.json` as the central manifest.
- Update this file (and AGENTS.md) when the workflow evolves.

