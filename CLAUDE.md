# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) backed by an in-memory store, used as the starter project for the Claude Code course.

## Commands

- `npm run dev` — start the API with auto-reload on http://localhost:3000 (`PORT` overrides)
- `npm test` — run all tests (Node's built-in `node:test` runner)
- `node --test tests/users.test.js` — run one test file
- `node --test --test-name-pattern="404"` — run tests whose name matches a pattern
- `npm run lint` — ESLint; CI (`.github/workflows/ci.yml`, Node 22) runs lint then tests, so both must pass

## Architecture

- `server.js` builds the Express app, mounts each router under its path, and exports `app`. It only calls `listen()` when run directly (`require.main === module`) — keep it that way so tests can import the app without opening a port.
- `routes/` holds one router file per resource; a new resource needs a new file here plus an `app.use("/<path>", ...)` line in `server.js`.
- `db/store.js` is the only data-access layer: an in-memory array that resets on restart and is shared across all tests in a run (tests that create users affect later reads).

## Conventions

- Use CommonJS (`require` / `module.exports`), not ES modules — ESLint is configured with `sourceType: "script"`.
- Always use named exports, never default exports — export an object (`module.exports = { foo, bar }`) rather than assigning a single value (`module.exports = foo`).
- Routes access data only through functions exported from `db/store.js`; never import or mutate the users array directly.
- Return errors as JSON `{ "error": "<message>" }` with the matching status (400 for invalid input, 404 for missing records); return 201 for successful creates.
- Write tests with `node:test` + `node:assert` and `supertest` against the exported `app`; don't add Jest/Mocha.
- Don't read or commit `.env`; document new config keys in `.env.example` instead.
