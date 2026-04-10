# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Public Claude Code plugin marketplace hosted at `Lanqu/claude-marketplace` on GitHub. Users subscribe with `/plugin marketplace add Lanqu/claude-marketplace` and install individual plugins from the catalog.

## Structure

- `.claude-plugin/marketplace.json` — marketplace catalog listing all available plugins
- `plugins/<name>/` — each plugin is a self-contained directory with its own `.claude-plugin/plugin.json`, skills, agents, hooks, and so on
- `README.md` — public-facing install instructions

## Adding a Plugin

1. Create `plugins/<plugin-name>/` with a valid `.claude-plugin/plugin.json` manifest
2. Add skills under `plugins/<plugin-name>/skills/<skill-name>/SKILL.md`
3. Add an entry to `.claude-plugin/marketplace.json` in the `plugins` array:
   ```json
   {
     "name": "<plugin-name>",
     "source": "./plugins/<plugin-name>",
     "description": "One-line description"
   }
   ```
4. Update `README.md` if the plugin needs setup instructions or dependencies

## Conventions

- Plugin names use kebab-case
- Each plugin must have a `description` in both its own `plugin.json` and the marketplace entry
- Bump `version` in `plugin.json` before releasing changes — Claude Code uses it to detect updates
- This is a public repository — no secrets, credentials, or internal references

## Git

- Remote uses `github.personal` SSH alias (personal key, not work)
- Single `master` branch
