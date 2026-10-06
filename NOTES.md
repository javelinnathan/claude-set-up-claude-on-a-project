# Notes

Working notes for setting up Claude Code on this project.

## What was set up
- `CLAUDE.md` — project overview, commands, architecture, and conventions for Claude.
- `.claude/settings.json` — shared permissions: allows test, lint, dev and read-only git commands; denies reading `.env`.

## Blocking `.env`
- `.claude/settings.json` has `"deny": ["Read(./.env)"]`, so Claude can't read the `.env` file, which may hold secrets.
- **Security risk without it:** `.env` typically holds API keys, database passwords, and other secrets. If Claude could read it, those values would enter the conversation context. From there Claude could accidentally expose them, for example by:
  - echoing them in a reply or a terminal log
  - hardcoding them into source files, tests, or docs
  - committing them to git
  - sending them to external services through tool calls

  Once a secret has leaked into a transcript, a commit, or a log, treat it as compromised and rotate it. The deny rule keeps the secrets out of Claude's context, so they can't leak this way.
- Deny rules take precedence over allow rules, so no other permission can re-enable reading it.
- `CLAUDE.md` backs this up: don't read or commit `.env`. New config keys go in `.env.example` instead.

## Verifying the setup
- `/memory` lists `CLAUDE.md` as a loaded memory file. Running it in this session opened `./CLAUDE.md`, which confirms the project file is picked up.
- `/permissions` shows the rules from `.claude/settings.json`:
  - Allow: `npm test`, `npm run lint`, `npm run dev`, `node --test:*`, `git status`, `git diff:*`, `git log:*`.
  - Deny: `Read(./.env)`.
  - The output of `/permissions` has not been checked in this session yet. Run it and confirm these rules appear.

## Things to remember
- CI runs lint then tests (Node 22); both must pass.
- Use CommonJS and named exports only.
- The in-memory store is shared across tests in a run.

## What I changed
- Added `node --test --test-name-pattern="404"` — run tests whose name matches a pattern to scan names that matches patterns
