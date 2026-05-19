## 1. E2e Harness Foundation

- [ ] 1.1 Create the `tests/e2e` directory structure for cases, fixtures, harness utilities, and target configuration.
- [ ] 1.2 Implement a minimal harness that accepts a packed axios tarball path and prepares an isolated temporary consumer environment.
- [ ] 1.3 Make the harness fail early with a clear error when the tarball is missing or cannot be installed/resolved.
- [ ] 1.4 Ensure temporary consumer state is cleaned up or isolated from checked-in fixture infrastructure.
- [ ] 1.5 Document local e2e harness usage and the intended distinction between unit, browser, and package-contract e2e tests.

## 2. Initial Package-Contract Cases

- [ ] 2.1 Add Node CJS cases for `require('axios')`, CJS exports, basic request behavior, headers, errors, cancellation, and representative HTTP adapter behavior.
- [ ] 2.2 Add Node ESM cases for `import axios from 'axios'`, named exports, unsafe exports where applicable, basic request behavior, headers, errors, cancellation, and representative ESM runtime behavior.
- [ ] 2.3 Add Bun cases for import resolution, package condition behavior, representative HTTP behavior, and fetch adapter behavior.
- [ ] 2.4 Add Deno cases for import resolution and representative fetch adapter behavior.
- [ ] 2.5 Add TypeScript e2e cases that compile representative CJS consumer code against `index.d.cts` with TypeScript 4.9.
- [ ] 2.6 Add TypeScript e2e cases that compile representative ESM consumer code against `index.d.ts` with TypeScript 5.x.

## 3. CI Integration

- [ ] 3.1 Update CI build flow to expose the packed axios tarball as the artifact consumed by e2e jobs.
- [ ] 3.2 Add Node CJS e2e jobs for the selected legacy Node versions.
- [ ] 3.3 Add Node ESM e2e jobs for the selected modern Node versions.
- [ ] 3.4 Add Bun and Deno e2e jobs that run against the same packed artifact or prepared package output.
- [ ] 3.5 Add TypeScript e2e jobs for CJS and ESM declaration compatibility.
- [ ] 3.6 Keep existing smoke, module, unit, and browser jobs running while the new e2e jobs are introduced.

## 4. Coverage Parity And Migration

- [ ] 4.1 Create a coverage parity checklist mapping existing `tests/smoke/*` cases to new e2e cases.
- [ ] 4.2 Create a coverage parity checklist mapping existing `tests/module/*` cases to new e2e type/package cases.
- [ ] 4.3 Port remaining smoke behavior for method aliases, instances, defaults, transforms, interceptors, serialization, timeouts, auth, FormData, progress, files or streams, and rate limiting where each behavior belongs in package-contract e2e.
- [ ] 4.4 Confirm existing browser tests remain in place and document which browser-specific behaviors are intentionally not moved to e2e.
- [ ] 4.5 Compare old and new suites in CI for failures, runtime, and diagnostic quality before removing old fixture projects.

## 5. Retire Redundant Fixture Infrastructure

- [ ] 5.1 Remove redundant `tests/smoke/cjs` fixture package files and cases after Node CJS e2e parity is documented.
- [ ] 5.2 Remove redundant `tests/smoke/esm` fixture package files and cases after Node ESM e2e parity is documented.
- [ ] 5.3 Remove redundant `tests/smoke/bun` fixture files and cases after Bun e2e parity is documented.
- [ ] 5.4 Remove redundant `tests/smoke/deno` fixture files and cases after Deno e2e parity is documented.
- [ ] 5.5 Remove redundant `tests/module/cjs` fixture package files and cases after CJS TypeScript e2e parity is documented.
- [ ] 5.6 Remove redundant `tests/module/esm` fixture package files and cases after ESM TypeScript e2e parity is documented.
- [ ] 5.7 Update package scripts and CI workflow commands to use the e2e suite names after redundant fixture projects are removed.

## 6. Verification

- [ ] 6.1 Run focused e2e harness checks locally against a freshly packed axios tarball.
- [ ] 6.2 Run `npm run lint`.
- [ ] 6.3 Run `npm run test:vitest:unit`.
- [ ] 6.4 Run `npm run test:vitest:browser:headless` to verify preserved browser coverage.
- [ ] 6.5 Run `npm run build` and `npm pack` before package-contract e2e verification.
- [ ] 6.6 Run the full CI e2e matrix and confirm each target consumes the packed artifact.
