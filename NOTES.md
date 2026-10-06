# Notes

Working notes for setting up Claude Code on this project.

## What was set up
- `CLAUDE.md` — project overview, commands, architecture, and conventions for Claude.
- `.claude/settings.json` — shared permissions: allows test, lint, dev and read-only git commands; denies reading `.env`.
- `.claude/skills/add-route` — skill for adding a new REST resource.

## Things to remember
- CI runs lint then tests (Node 22); both must pass.
- Use CommonJS and named exports only.
- The in-memory store is shared across tests in a run.
