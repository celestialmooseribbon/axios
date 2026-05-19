## Why

The package-contract e2e suite will provide stronger CI coverage, but contributors also need a practical way to reproduce selected matrix targets locally without installing every Node, Bun, Deno, and TypeScript runtime on the host machine. A Docker-backed local runner gives maintainers a CI-like execution path for targeted debugging and full-matrix confidence while keeping the e2e suite itself focused on the packed axios package contract.

## What Changes

- Add Docker-backed local execution for the package-contract e2e suite introduced by `add-package-contract-e2e-suite`.
- Reuse the same e2e harness and target matrix as CI so local Docker runs exercise the same packed-tarball consumer contract.
- Add commands for targeted Docker runs, such as a single Node version, Bun, Deno, TypeScript target, or the full e2e matrix.
- Keep host-mode e2e execution available for quick local iteration; Docker is the reproducibility path, not the only way to run e2e cases.
- Document Docker prerequisites, target selection, Apple Silicon/old Node image caveats, and expected cleanup behavior.
- Do not change axios runtime APIs, package exports, supported environments, or package-manager strategy.
- Do not introduce Docker as a requirement for normal unit or browser test development.

## Capabilities

### New Capabilities
- `docker-e2e-local-runner`: Defines Docker-backed local execution for package-contract e2e targets using isolated runtime containers and the packed axios artifact.

### Modified Capabilities
- None.

## Impact

- Affected test areas: `tests/e2e` harness, e2e matrix configuration, local test scripts, and e2e documentation.
- Affected CI: none required beyond maintaining parity with the e2e matrix; CI remains the authoritative full validation path.
- Affected package behavior: none intended.
- Affected public API and type declarations: none.
- Dependencies: no new runtime dependencies. Docker is an optional local tool, not a package dependency.
- Security: Docker runs must avoid mounting secrets, npm credentials, the Docker socket, or other host-sensitive paths into containers; package installation safeguards such as `ignore-scripts` must remain explicit where applicable.
