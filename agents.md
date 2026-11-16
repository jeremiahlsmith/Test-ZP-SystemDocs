# AGENTS.md

Instructions for AI coding agents working in this repository. Read this file and `README.md` before making any changes.

## Project Summary

Orders Service: a Node.js 20 / Express API backed by PostgreSQL, Redis, and Kafka. See `README.md` for architecture, configuration, and runbooks.

## Source of Truth

| Topic                                 | Where it lives                                            |
| ------------------------------------- | --------------------------------------------------------- |
| Architecture, setup, config, runbooks | `README.md`                                               |
| API contract                          | `docs/openapi.yaml`                                       |
| Environment variables                 | `.env.example` and the Configuration table in `README.md` |
| Agent rules and conventions           | `AGENTS.md` (this file)                                   |

If code and docs disagree, flag the discrepancy and fix the docs to match verified behavior. Do not guess.

## Setup and Commands

```bash
npm install            # install dependencies
npm run dev            # start local server on :3000
npm test               # unit tests
npm run test:integration
npm run lint           # must pass before committing
npm run migrate        # apply database migrations
```

Local dependencies start with `docker compose up -d`.

## Repository Layout

```
src/
  routes/        # HTTP handlers (thin, no business logic)
  services/      # business logic
  repositories/  # database access
  events/        # Kafka producers and consumers
  config/        # env parsing and validation
migrations/      # SQL migrations (never edit existing ones)
docs/            # openapi.yaml and supporting docs
tests/           # unit/ and integration/
```

## Code Conventions

- TypeScript strict mode; no `any` without a comment explaining why.
- Business logic goes in `services/`, never in route handlers.
- All database access goes through `repositories/`.
- Validate all external input at the route boundary.
- Use structured logging (`logger.info({ orderId }, "message")`); never `console.log`.
- Write or update tests for every behavior change.

## Self-Documentation Rules

Docs are part of the change. Before finishing any task, update docs as follows:

| If you change...                           | Then update...                                                 |
| ------------------------------------------ | -------------------------------------------------------------- |
| An endpoint, request, or response shape    | `docs/openapi.yaml` and the API Reference table in `README.md` |
| An environment variable                    | `.env.example` and the Configuration table in `README.md`      |
| Infrastructure, dependencies, or data flow | The Architecture section and diagram in `README.md`            |
| Deployment steps or CI workflows           | The Deployment section in `README.md`                          |
| Alerts, metrics, or dashboards             | The Monitoring & Alerts section in `README.md`                 |
| A failure mode you diagnosed or fixed      | Add an entry to Runbook or Troubleshooting in `README.md`      |
| Repo structure, commands, or conventions   | This file (`AGENTS.md`)                                        |

After updating, set the "Last reviewed" date in `README.md` to today's date.

## Boundaries

**Always**
- Run `npm run lint` and `npm test` before declaring a task complete.
- Keep changes small and focused on the task.
- Follow existing patterns in neighboring files.

**Ask first**
- Adding or upgrading dependencies.
- Changing database schemas (create a new migration; never edit old ones).
- Changing public API behavior in a breaking way.
- Modifying CI/CD workflows or Kubernetes manifests.

**Never**
- Commit secrets, tokens, or `.env` files.
- Edit generated files or existing migrations.
- Disable tests or lint rules to make a check pass.
- Push directly to `main` or create release tags.
- Invent endpoints, config values, or commands that are not in the repo.

## Workflow

1. Read `README.md` and the files relevant to the task.
2. State a brief plan before editing for any non-trivial change.
3. Make the change with tests.
4. Update docs per the Self-Documentation Rules.
5. Run lint and tests.
6. Summarize what changed, which docs were updated, and anything left unresolved.

## Commit and PR Style

- Commit format: `type(scope): short summary` (e.g. `feat(orders): add cancel endpoint`).
- Types: `feat`, `fix`, `docs`, `refactor`, `test`, `chore`.
- PR descriptions include: what changed, why, how it was tested, and which docs were updated.

## When Uncertain

- Prefer asking a clarifying question over making assumptions about requirements.
- If you cannot verify something (a command, a value, a behavior), say so explicitly instead of guessing.
- If you notice outdated or conflicting documentation unrelated to your task, mention it in your summary rather than silently changing it.