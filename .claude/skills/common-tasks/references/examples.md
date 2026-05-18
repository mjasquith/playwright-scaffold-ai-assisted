# Common-Tasks Worked Examples

Three end-to-end walkthroughs showing how to combine a `common-tasks` template with the 8-phase `ai-native-workflow` and the relevant specialized skills. Phase numbering aligns with `ai-native-workflow/SKILL.md`.

## Example 1: Add a new page object and a functional test

User says: _"Add a page object and a functional smoke test for the settings page at /settings."_

1. **Phase 1 — Classify** — Codegen, two artifacts (page object + functional test).
2. **Phase 2 — Route** — `common-tasks` (template) → `page-objects` + `test-standards` (deep rules).
3. **Phase 3 — Explore** — Run `ls pages/` and `ls tests/` to resolve `{area}`. Run `playwright-cli goto /settings` then `snapshot` to discover roles, labels, forms, buttons. (`playwright-cli` is the only sanctioned UI explorer — see `CLAUDE.md` "No Substitute UI Exploration".)
4. **Phase 4 — Plan + Confidence** — Copy **Add a New Page Object (With Exploration)** + **Create a Functional Test** templates; fill `[PAGE NAME]`, `[URL]`, `[FEATURE]`, scenarios. Output Confidence + Unknowns.
5. **Phase 5 — Human gate** — Confirm.
6. **Phase 6 — Apply** — `getByRole` first, no JSDoc on getters, single `@smoke` tag, factory data only. Register page object in `fixtures/pom/page-object-fixture.ts`.
7. **Phase 7 — Verify** — Walk the verification checklist; run `npx playwright test tests/{area}/functional/settings.spec.ts`.
8. **Phase 8 — Report + commit** — _"Add SettingsPage page object and @smoke functional test"_.

## Example 2: Add complete API coverage for a new endpoint

User says: _"Add API tests for `POST /api/products`."_

1. **Phase 1 — Classify** — Codegen, two artifacts (Zod schema + API spec file).
2. **Phase 2 — Route** — `common-tasks` (templates) → `api-testing` + `type-safety` (deep rules).
3. **Phase 3 — Explore** — `ls fixtures/api/schemas/`, `ls tests/`, `ls enums/`. Source contract from OpenAPI / Swagger; only fall back to live-HTTP exploration if no documentation exists.
4. **Phase 4 — Plan + Confidence** — Copy **Create a New Zod Schema (From Documentation)** + **Add API Test** templates. Build coverage plan listing every status code from the spec for this endpoint, stating what test will cover each. Present plan before generating code.
5. **Phase 5 — Human gate** — Confirm coverage plan.
6. **Phase 6 — Apply** — Follow `api-testing` Phases 1–8 (contract → schema → happy path → `test.step` → full status-code matrix → per-field negative coverage with `INVALID_STRING_VALUES` / `INVALID_NUMBER_VALUES` from `test-data/static/util/invalid-values.ts` → behavior-mismatch protocol → helper fixture if reused). Apply Critical: `z.strictObject()`, `expect(Schema.parse(body)).toBeTruthy()`, `ApiEndpoints.*`, `process.env.*`, `@api` tag.
7. **Phase 7 — Verify** — Walk the verification checklist with particular attention to coverage-audit and auth-matrix boxes. Run the new spec; any remaining red is a bug → `api-testing` Phase 7 (`test.skip` + `// FIXME:`).
8. **Phase 8 — Report + commit** — _"Add POST /api/products tests with full coverage matrix"_.

## Example 3: Add a destructive factory-driven test

User says: _"Add a regression test that creates and deletes a user via the UI."_

1. **Phase 1 — Classify** — Codegen, possibly three artifacts (factory if missing + functional test + helper fixture candidate).
2. **Phase 2 — Route** — `common-tasks` (templates) → `data-strategy` + `test-standards` (deep rules); `fixtures` / `helpers` only if the setup will be reused across 3+ files.
3. **Phase 3 — Explore** — `ls test-data/factories/`, `ls tests/`. Confirm whether `generateUser()` already exists.
4. **Phase 4 — Plan + Confidence** — Copy **Create a New Data Factory** (if missing) + **Create a Functional Test** templates. Output Confidence + Unknowns.
5. **Phase 5 — Human gate** — Confirm.
6. **Phase 6 — Apply** — Factory data, single `@destructive` tag (overrides any other importance tag — `@destructive` is heaviest and wins), no hardcoded strings. Wire `afterEach` / `afterAll` cleanup that runs even on failure.
7. **Phase 7 — Verify** — Checklist emphasis: `@destructive` is the only tag, teardown in place, no hardcoded credentials. Run the file; confirm the run leaves no orphan users behind.
8. **Phase 8 — Report + commit** — _"Add @destructive regression test for user create + delete"_.
