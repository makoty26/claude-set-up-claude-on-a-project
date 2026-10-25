# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

A small Express REST API (users + health check) with an in-memory data store, used as the base project for the Claude Code course.

## Commands

- `npm run dev` — start the API on http://localhost:3000 with `node --watch` (port from `PORT`, see `.env.example`)
- `npm test` — run all tests with the built-in Node test runner (`node --test`)
- `node --test tests/users.test.js` — run one test file
- `node --test --test-name-pattern="POST /users"` — run tests whose name matches
- `npm run lint` — ESLint (`eslint:recommended`)

CI (`.github/workflows/ci.yml`, Node 22) runs `npm run lint` then `npm test` on every push and PR — both must pass.

## Conventions

- Use CommonJS (`require` / `module.exports`), not ES modules — ESLint is configured with `sourceType: "script"`.
- Use double quotes and semicolons, matching existing files.
- Write tests with `node:test` + `node:assert` and `supertest`, not Jest/Mocha. Import `app` from `server.js` and call `request(app)`; never start a real server in tests.
- Route handlers never touch data directly — go through functions exported from `db/store.js`.
- Return errors as JSON `{ error: "<message>" }` with the matching status (400 for invalid input, 404 for missing resources); return 201 with the created object on POST.
- Parse `:id` params with `Number(...)` before calling the store — store IDs are numbers.
- The app code is not meant to change as part of the course setup tasks; only touch `server.js`, `routes/`, or `db/` when explicitly asked.

## Architecture

- `server.js` builds the Express app, applies `express.json()`, mounts routers, and exports `app`. It only calls `app.listen` when run directly (`require.main === module`) so tests can import it.
- `routes/` — one `express.Router` file per resource, mounted at `/<resource>` in `server.js`. Adding a resource means a new route file plus a `app.use(...)` line.
- `db/store.js` — module-level in-memory arrays standing in for a database. Data resets on restart, and state is shared across all tests in a process, so tests that create data can affect later ones.
