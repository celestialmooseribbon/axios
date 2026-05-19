## Context

Axios currently has strong source-level unit coverage and browser-specific tests, plus separate smoke and module suites that test the packaged output. The smoke and module suites are implemented as long-lived fixture projects under `tests/smoke/*` and `tests/module/*`, each with its own package manifest, lockfile, dependencies, and runner setup.

Those fixture projects validate important public surfaces, but they also duplicate infrastructure and make compatibility coverage look like package-management state rather than package-contract behavior. This change introduces a dedicated `tests/e2e` suite that treats the packed axios tarball as the artifact under test and uses temporary consumer projects for each target environment.

## Goals / Non-Goals

**Goals:**
- Validate the packed axios package as a real consumer would install and execute it.
- Cover public runtime, packaging, and type surfaces across Node CJS, Node ESM, Bun, Deno, browser, and TypeScript consumer targets.
- Keep existing unit tests and browser tests in place.
- Run the new e2e suite in parallel with current smoke/module suites until coverage is proven equivalent or better.
- Remove redundant smoke/module fixture-project infrastructure only after parity is established.
- Keep e2e cases organized by package-contract behavior rather than by permanent package fixture.

**Non-Goals:**
- No axios runtime behavior changes.
- No package export, declaration, or supported-runtime changes except where tests reveal an existing bug that should be handled separately.
- No package-manager migration work.
- No removal of browser tests.
- No broad replacement of unit tests with e2e tests.

## Decisions

### Decision: Test The Packed Tarball As The Boundary

The e2e suite will run against an `npm pack` artifact rather than importing axios source files from `lib/` or the workspace root.

Rationale: package-contract regressions often live in `package.json` exports, built `dist/` files, type declarations, included files, and runtime condition resolution. Source imports cannot validate those surfaces.

Alternative considered: keep testing source files directly from a shared runner. This is faster, but it overlaps with unit tests and does not prove the published package shape.

### Decision: Use Temporary Consumer Projects

CI and local harness commands will create temporary consumer projects, install the packed tarball, run selected cases, and clean up afterward.

Rationale: temporary projects preserve consumer realism without checking in one package manifest and lockfile per target. They also allow environment-specific setup to live in the runner instead of permanent fixture directories.

Alternative considered: keep permanent fixture projects and consolidate their scripts. This reduces churn but leaves the original maintenance and lockfile problems in place.

### Decision: Keep Native Runtime Assertions Where Practical

Node e2e cases should prefer built-in assertions. Bun and Deno cases can use their native test APIs when they add value. Extra test dependencies should be avoided unless they materially improve signal or maintainability.

Rationale: package-contract tests should primarily validate axios, not the ability to install and run a test framework in every compatibility target.

Alternative considered: standardize on one test runner. This is simpler conceptually, but it reintroduces test-runner compatibility constraints, especially for legacy Node targets and non-Node runtimes.

### Decision: Preserve Browser Tests Separately

The existing browser tests remain part of the test strategy. The e2e suite may add package-level browser checks later, but this change does not delete browser-specific test coverage.

Rationale: browser behavior includes XHR, fetch, XSRF, cookies, same-origin checks, progress events, and browser bundle resolution. Those concerns are valuable enough to keep as a focused suite while package-contract e2e matures.

Alternative considered: fold browser coverage into e2e immediately. This would make the migration larger and risk losing nuanced browser-specific coverage.

### Decision: Retire Existing Smoke/Module Projects Only After Parity

The current `tests/smoke/*` and `tests/module/*` suites will continue running while the e2e suite is introduced. Redundant fixtures are removed only after the e2e matrix covers the same public surfaces.

Rationale: running in parallel lets us compare failures, coverage, and runtime cost before deleting established compatibility checks.

Alternative considered: replace smoke/module suites in one step. This is faster but increases regression risk and makes it harder to prove the new suite covers the old behavior.

## Risks / Trade-offs

- E2e matrix runtime could grow quickly → Keep cases contract-focused, run targets in parallel, and avoid duplicating unit-level branch coverage.
- Temporary project setup could become opaque → Keep the harness small, document the matrix, and make each generated project explicit in logs.
- Coverage parity could be subjective → Maintain a coverage checklist mapping old smoke/module cases to new e2e cases before deleting old fixtures.
- Native assertions reduce runner features → Prefer simple, deterministic cases with clear failure messages; add a test dependency only when native tooling is insufficient.
- Packed-tarball tests require build and pack steps → Reuse the existing CI build artifact flow and fail e2e early if no tarball is available.
- Browser package e2e could overlap with existing browser tests → Treat browser e2e as package-resolution coverage and keep adapter/browser behavior in the existing browser suite.

## Migration Plan

1. Add the `tests/e2e` structure and minimal harness for running cases against a packed tarball.
2. Add initial cases for import/export, basic requests, headers, errors, cancellation, and type compatibility.
3. Add CI jobs that run e2e targets from the packed artifact in parallel with existing smoke/module suites.
4. Port remaining smoke/module coverage into e2e cases and track parity in documentation or a checklist.
5. Compare failures and runtime cost across old and new suites.
6. Remove redundant smoke/module fixture projects after parity is established.
7. Update test documentation and contributor guidance to describe the e2e suite and when to add cases.

## Open Questions

- Which browser package-contract checks, if any, should live in `tests/e2e` versus the existing browser suite?
- Should local e2e execution expose one command per target, one matrix command, or both?
- What is the minimum old-suite-to-new-suite parity checklist required before deleting each smoke/module fixture project?
