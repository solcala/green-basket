# Green Basket

A shared family grocery list you can actually use — and a small, testable portfolio app that shows off TypeScript, React, API testing and CI.

> **Status:** Planning / Day 1 scaffolding next. The folders and run commands below are the target layout; they are not in the repo yet.

## Goal

A shared, responsive grocery list where family members add, check off and plan items, and can turn recipes into shopping lists.

## Stack

- **Frontend:** Vite + React + TypeScript + Tailwind CSS (responsive web app)
- **API:** Express + TypeScript, Zod validation, SQLite (local dev and tests)
- **Tests:** Playwright API and E2E tests, with Zod schema checks and Faker data
- **CI:** GitHub Actions; both suites run on every push, and the Playwright HTML report is uploaded as an artifact
- **Hosting (free to start):** frontend on Vercel, API on Render's free plan (exact URLs once deployed)
- **Storage:** Render's free plan wipes local files on restart and redeploy, so the deployed API uses [Turso](https://turso.tech) (hosted, SQLite-compatible, free tier) instead of a local SQLite file.

## Roadmap

### Phase 1 (week 1) — this repo's first shippable slice

- Shared lists and items: add, check off, delete
- Recipes with “add ingredients to the list”
- A simple family member picker (no real auth)
- Playwright API + E2E tests and GitHub Actions CI
- Deploy the frontend and API to the free hosts above

### Phase 2

- Home pantry, auto-filled from checked-off shopping items, plus a manual “used up” button
- Recipe suggestions ranked by how many ingredients you already have, via [TheMealDB](https://www.themealdb.com/api.php)
- Optional LLM recipe suggestions, with the model call mocked in tests

### Phase 3

- Upload a receipt photo and get suggested list items (OCR), with the image service mocked in tests

## Project structure

```
client/          # Vite + React + TypeScript frontend
server/          # Express + TypeScript API + SQLite
tests/api/       # Playwright API tests (Zod + Faker)
tests/e2e/       # Playwright E2E tests (page objects, data-testid)
docs/            # Screen mockups and short decision notes
```

Planning lives in the repo: this README, GitHub Issues + a Projects board, and notes under `docs/`.

## How to run

Scaffolding is not in the repo yet. After Day 1, expect roughly:

```bash
cd server && npm install && npm run dev
cd client && npm install && npm run dev
npx playwright test tests/api   # from the repo root
npx playwright test tests/e2e   # from the repo root
```

Exact scripts are TBD.

## Testing strategy

- **Why this stack:** API and UI tests validate request and response bodies against the same Zod contracts the server uses, so a change that leaves a schema out of date fails in CI. This proves schema conformance, not compatibility with an independent client. Playwright covers both layers, so one runner, one report and one CI job cover API and E2E.
- **API tests** hit the real Express server and an isolated SQLite database. Request and response bodies are checked against the same Zod schemas the API uses. Faker generates names, quantities and recipe data so tests are not tied to a fixed fixture set.
- **E2E tests** use Playwright page objects and `data-testid` selectors. A core scenario is two family members (via the member picker) editing the same list: add, check off, and verify updates after a refresh (no realtime/WebSockets in Phase 1).
- **CI** runs both suites on GitHub Actions and uploads the Playwright HTML report as an artifact. External services added in later phases (TheMealDB, an LLM, OCR) are mocked so the pipeline stays deterministic.
