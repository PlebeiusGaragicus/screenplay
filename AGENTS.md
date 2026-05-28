# AGENTS.md — Screenplay / Comic Generation Project

## Overview

This repository is a template for creating AI-generated comic books and/or videos. It uses a custom Fountain-flavored markdown format with wiki-style links to connect screenplays in `./story/` with visual prompts (characters, locations, etc.) in `./prompts/`. 

All content files utilize YAML frontmatter, and the project's state is managed via a central `screenplay.json` manifest at the root.

## Roles
- **Human**: Maintains high-level documentation and general oversight.
- **Agent**: Manages and creates all other repository content (story, prompts, screenplay.json) and updates this file (`AGENTS.md`).

## Core Rules
- All story/*.md and prompts/*.md files use Fountain format + `[[wiki-style links]]`.
- Include YAML frontmatter at the top of every content file.
- Include YAML frontmatter at the top of every content file.
- Keep screenplay.json in sync (characters, locations, prompts paths, version bumps).
- Story files live in ./story/; reusable prompts/descriptions in ./prompts/ (with subdirs for characters/, locations/, styles/, etc.).
- Generated/reference images go in ./images/.

## Workflow
1. When adding scenes/characters/locations, create matching .md files with YAML frontmatter + Fountain content and `[[links]]`.
2. Update screenplay.json "prompts" map and top-level arrays.
3. Use consistent naming: kebab-case for files, Title Case for keys.
4. Prefer short, focused edits; run validation/linting if scripts exist.

Update this file when new conventions or tooling are introduced.
