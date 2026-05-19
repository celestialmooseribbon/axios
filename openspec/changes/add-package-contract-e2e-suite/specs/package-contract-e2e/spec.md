## ADDED Requirements

### Requirement: Packed Package Boundary
The e2e suite SHALL execute axios from a packed package artifact rather than from source-tree imports.

#### Scenario: E2e target installs packed axios
- **WHEN** an e2e target runs
- **THEN** the target uses an axios tarball produced by the build-and-pack step as the package under test

#### Scenario: Source imports are avoided
- **WHEN** an e2e case imports axios
- **THEN** the import resolves through the installed package name or runtime package mapping instead of direct `lib/` or repository-relative source paths

### Requirement: Temporary Consumer Environments
The e2e suite SHALL run package-contract cases inside temporary consumer environments for each selected target.

#### Scenario: Consumer project setup
- **WHEN** a Node, Bun, Deno, browser, or TypeScript e2e target starts
- **THEN** the harness creates or prepares an isolated consumer environment for that target

#### Scenario: Consumer project cleanup
- **WHEN** an e2e target completes successfully or fails
- **THEN** temporary project state is cleaned up or isolated so it does not become checked-in fixture infrastructure

### Requirement: Environment Matrix Coverage
The e2e suite SHALL cover the selected package-consumer surfaces across runtime and type targets.

#### Scenario: Node CJS package contract
- **WHEN** the Node CJS e2e target runs
- **THEN** it verifies `require('axios')`, CommonJS exports, and representative Node adapter behavior from the packed package

#### Scenario: Node ESM package contract
- **WHEN** the Node ESM e2e target runs
- **THEN** it verifies `import axios from 'axios'`, named ESM exports, unsafe exports where applicable, and representative ESM runtime behavior from the packed package

#### Scenario: Bun package contract
- **WHEN** the Bun e2e target runs
- **THEN** it verifies Bun can install or resolve the packed package and execute representative Bun import, HTTP, and fetch behavior

#### Scenario: Deno package contract
- **WHEN** the Deno e2e target runs
- **THEN** it verifies Deno can execute representative axios import and fetch behavior using the package artifact or prepared package output

#### Scenario: TypeScript package contract
- **WHEN** TypeScript e2e targets run
- **THEN** they compile representative CJS and ESM consumer code against the shipped `index.d.cts` and `index.d.ts` declarations

### Requirement: Functional Surface Coverage
The e2e suite SHALL include contract-level cases for key public axios behavior without duplicating unit-level branch coverage.

#### Scenario: Core behavior surfaces are covered
- **WHEN** the e2e suite reaches coverage parity
- **THEN** it includes cases for imports, method aliases, instances, defaults, headers, params or body serialization, transforms, interceptors, cancellation, errors, and representative adapter behavior

#### Scenario: Node-specific behavior surfaces are covered
- **WHEN** Node e2e targets reach coverage parity
- **THEN** they include cases for representative HTTP adapter behavior, streams or files where relevant, FormData, timeouts, auth, and package export resolution

#### Scenario: Browser-specific coverage remains preserved
- **WHEN** this change is implemented
- **THEN** existing browser tests continue to cover XHR, fetch, XSRF, cookies, same-origin checks, progress events, and browser-specific adapter behavior

### Requirement: Parallel Migration
The e2e suite SHALL run alongside existing smoke and module suites until coverage parity is established.

#### Scenario: Existing suites remain during initial e2e rollout
- **WHEN** initial e2e jobs are added to CI
- **THEN** existing smoke and module jobs continue to run

#### Scenario: Redundant fixture projects are retired after parity
- **WHEN** e2e coverage is documented as equivalent or better for a smoke or module fixture project
- **THEN** that redundant fixture project can be removed as part of the migration

### Requirement: Minimal Test Infrastructure Dependencies
The e2e suite SHALL avoid unnecessary long-lived package manifests, lockfiles, and test-runner dependencies for compatibility targets.

#### Scenario: Native assertions are sufficient
- **WHEN** a target runtime provides adequate assertion or test primitives
- **THEN** e2e cases use those primitives instead of adding a target-local test runner dependency

#### Scenario: Test dependency is necessary
- **WHEN** an e2e target requires an additional test-only dependency
- **THEN** the dependency is isolated to the e2e harness or generated consumer environment and is documented with its purpose

### Requirement: CI Artifact Flow
CI SHALL build and pack axios once, then use that artifact for package-contract e2e jobs.

#### Scenario: E2e jobs consume build artifact
- **WHEN** CI runs package-contract e2e jobs
- **THEN** each job downloads or receives the same packed axios artifact from the build step

#### Scenario: E2e job fails without artifact
- **WHEN** the packed axios artifact is missing or cannot be installed by a target
- **THEN** the e2e job fails before running behavior cases
