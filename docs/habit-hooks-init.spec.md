# `habit-hooks init`

## Audience
- You: A maintainer or user of `habit-hooks` setting up a project.
- Goal: Understand what `init` does, see what it writes, and know how to act on its output.

## Expectation
- You expect `init` to set up your project, detect languages, enable the right plugins, and tell you if anything is missing.
- You expect `init` to be idempotent (safe to run again).

## How it works

### 1. Setup and Detection
`init` detects the languages in your project and writes `.habit-hooks/config.toml`. It enables a plugin for each language, putting `generic` last. It also reports any missing tools or plugins.

**Prerequisites for this spec:**
- Plugins are vendored in `.habit-hooks/<name>/`.
- `GIT_CEILING_DIRECTORIES` is set to prevent git from walking up the directory tree during tests.

**Test Case: Project with no config**
- Input: `pyproject.toml` with `name = "acme"`.
- Action: `habit-hooks init`
- Expected Output:
  - Detects Python.
  - Writes `.habit-hooks/config.toml` with `plugins = ["python", "generic"]`.
  - Reports no missing tools.