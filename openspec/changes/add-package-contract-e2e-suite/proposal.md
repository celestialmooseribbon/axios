## Why

The current smoke and module compatibility suites are implemented as multiple permanent fixture projects, each with its own package manifest, lockfile, test runner, and dependency graph. This makes compatibility coverage harder to reason about, duplicates infrastructure across runtimes, and couples package-contract testing to fixture-project maintenance rather than the actual published axios package boundary.

We need a package-contract e2e suite that runs against the packed axios tarball in real consumer environments while preserving the existing unit and browser test suites. This gives stronger regression confidence for public runtime, packaging, and type surfaces without requiring long-lived mini-projects for every compatibility target.

## What Changes

- Add a new `tests/e2e` package-contract test suite that executes against the packed axios tarball, not source files.
- Model e2e cases by public behavior surface and target environment rather than by permanent fixture package.
- Add CI jobs that create temporary consumer projects, install the packed tarball, run the selected e2e cases, and clean up.
- Run the new e2e suite in parallel with the existing smoke and module suites until coverage is equivalent or better.
- Preserve the current unit tests and browser tests; browser-specific behavior remains covered and is not removed by this change.
- After e2e coverage is proven, retire redundant `tests/smoke/*` and `tests/module/*` fixture-project infrastructure in a follow-up step within this change.
- Do not change axios runtime APIs, package exports, type declarations, or supported environments as part of this work.
- Do not include package-manager migration work in this change.

## Capabilities

### New Capabilities
- `package-contract-e2e`: Defines package-contract e2e coverage for the packed axios package across Node CJS, Node ESM, Bun, Deno, browser, and TypeScript consumer surfaces.

### Modified Capabilities
- None.

## Impact

- Affected test areas: `tests/e2e`, `tests/smoke`, `tests/module`, existing unit/browser test commands, and test documentation.
- Affected CI: workflows that build, pack, upload, install, and test axios artifacts across environment matrices.
- Affected package behavior: none intended; the suite validates existing package behavior.
- Affected public API: none.
- Affected type declarations: none intended; e2e type cases validate existing `index.d.ts` and `index.d.cts` behavior.
- Dependencies: no new runtime dependencies. Any test-only dependency must be justified by the e2e harness design and should be avoided where native runtime assertions are sufficient.
- Security: the suite strengthens release confidence by testing the packed tarball and published package shape; it must not weaken existing `ignore-scripts` safeguards or introduce secret-bearing CI contexts.
