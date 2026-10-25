# Notes

## What's in CLAUDE.md and why

I kept `CLAUDE.md` to the four parts the project needs: a one-line description, commands, conventions, and architecture.

- **Commands:** `dev`, `test` and `lint`, plus how to run one test file or one test by name (`node --test --test-name-pattern=...`). That last one isn't obvious from `package.json`, and it saves running the whole suite.
- **Conventions:** only rules that can't be guessed safely from one file. Use CommonJS, not ESM (ESLint sets `sourceType: "script"`). Use `node:test` + `supertest`, not Jest. Routes get data only through `db/store.js`. Errors are returned as `{ error }` with a 400 or 404. Don't touch app code unless asked.
- **Architecture:** the details you'd otherwise only find by reading several files. `server.js` exports `app` and only listens when run directly, which is how tests use it. Each resource has one router. The in-memory store keeps its state for the whole test run.

What I left out: a file-by-file listing (`ls` shows it), generic advice ("write clean code"), the course setup instructions from the README (one-off tasks, not project knowledge), and anything from `.env`.

## Permission rules

- **allow:** `npm test`, `npm run lint`, `node --test`. These are read-only checks I run constantly, so being asked every time only slows things down.
- **ask:** `git push`. It's outward-facing, so I want to confirm each one.
- **deny:** reading or editing `.env`, and `git push --force` / `git push -f`.

Without the `.env` deny rule, Claude could read real secrets (such as a `DATABASE_URL` with a password) into the conversation, or copy them into code or a commit. Without the force-push rule, one wrong command could overwrite the shared branch history for everyone. Deny rules win over the `git push` ask rule. They match by prefix, though, so a variant like `git push origin main --force` isn't caught by the deny list; the `ask` rule still makes it ask before running.

## Verification

- `/memory`: 実行し、`CLAUDE.md` がプロジェクトのコンテキストとして正しく読み込まれていることを確認しました。
- `/permissions`: 実行し、テストコマンドの許可（allow）ルールと、`.env` ファイルの読み取り拒否（deny）ルールが設定されていることを確認しました。
