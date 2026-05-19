## Context

The `add-package-contract-e2e-suite` change introduces a package-contract e2e suite that runs against a packed axios tarball in temporary consumer environments. That suite defines the test boundary and CI matrix, but local reproduction still needs a practical execution strategy for maintainers who do not have every target runtime installed.

This fast-follow change adds Docker-backed local execution for the same e2e targets. Docker is used to reproduce target runtimes such as old Node versions, current Node versions, Bun, and Deno without turning those runtimes into host-machine prerequisites.

## Goals / Non-Goals

**Goals:**
- Provide a Docker-backed local path for running selected package-contract e2e targets.
- Reuse the same e2e harness, cases, and target matrix as CI wherever practical.
- Allow targeted local debugging, such as one Node version or one runtime family.
- Allow full local matrix execution for maintainers who want CI-like confidence before pushing.
- Keep Docker optional for normal development and quick host-mode e2e runs.
- Keep Docker execution isolated from credentials, host Docker socket access, and checked-in fixture state.

**Non-Goals:**
- No changes to axios runtime behavior, package exports, or supported environments.
- No replacement of CI as the authoritative validation environment.
- No requirement that contributors use Docker for unit tests, browser tests, or routine development.
- No package-manager migration work.
- No custom long-lived Docker image publishing unless later justified by performance or reproducibility needs.

## Decisions

### Decision: Docker Wraps The Existing E2e Harness

Docker commands will invoke the same e2e runner used by host-mode and CI execution rather than introducing a separate Docker-only test path.

Rationale: one harness reduces drift. Docker should provide runtime isolation, not a parallel implementation of package-contract testing.

Alternative considered: write target-specific Docker scripts. This is straightforward initially, but it creates duplicated target logic and makes CI/local behavior diverge over time.

### Decision: Use Matrix Targets, Not Ad Hoc Image Names

Docker local runs will select named e2e targets from the shared matrix, such as `node-cjs-12`, `node-esm-24`, `bun`, `deno`, or `types-cjs-ts49`.

Rationale: named targets make local commands match CI concepts and allow documentation to focus on behavior rather than container details.

Alternative considered: expose raw image and command arguments. This is flexible but too easy to run a target differently from CI.

### Decision: Pull Standard Runtime Images By Default

The local runner will use standard upstream runtime images for Node, Bun, and Deno where possible. A custom project image is out of scope unless standard images prove insufficient.

Rationale: standard images reduce maintenance and avoid committing the project to container image publishing.

Alternative considered: maintain axios-specific images. This could speed repeated runs, but it adds release, cache, and security review overhead.

### Decision: Docker Mode Uses The Packed Artifact

Docker mode will require or create a packed axios tarball and pass that artifact into each container target.

Rationale: Docker local runs must preserve the same package boundary as CI e2e. Running source files inside containers would weaken the purpose of the suite.

Alternative considered: mount the repository and import directly from the workspace. This is simpler, but it tests source layout instead of the published package contract.

### Decision: Restrict Host Mounts And Credentials

Docker runs will mount only the repository or necessary e2e artifacts, avoid mounting host credential directories, and never mount the host Docker socket.

Rationale: axios already treats dependency execution and maintainer workstation exposure as security-sensitive. Docker e2e should improve isolation, not create a new path for credential exposure.

Alternative considered: broad home-directory mounts for cache reuse. This improves speed but risks leaking npm, GitHub, SSH, or cloud credentials into test containers.

### Decision: Document Apple Silicon Caveats

The runner and documentation will call out that older Node images may require an explicit platform such as `linux/amd64` on Apple Silicon and may run slower under emulation.

Rationale: legacy Node coverage is important, but local reproducibility should set expectations clearly.

Alternative considered: hide platform selection entirely. This is cleaner until image resolution fails or runs with a different architecture than CI.

## Risks / Trade-offs

- Docker startup and image pulls can be slow -> Support targeted target selection and keep host-mode e2e available for quick loops.
- Local Docker may still differ from GitHub Actions -> Reuse the same matrix and treat CI as authoritative for final validation.
- Old Node images can be slow or awkward on Apple Silicon -> Document platform selection and allow users to run only affected targets when needed.
- Broad mounts could expose credentials -> Use minimal mounts, avoid Docker socket access, and document the security model.
- Standard runtime images could change over time -> Prefer explicit major-version images and keep CI/local matrix definitions reviewable.
- Full matrix local runs may be expensive -> Make full matrix opt-in and keep per-target runs easy.

## Migration Plan

1. Extend the e2e matrix with Docker image metadata for each target.
2. Add local Docker runner commands that select one target, a target group, or the full matrix.
3. Ensure Docker mode consumes the packed axios tarball and uses the same temporary consumer setup as host/CI e2e.
4. Add safeguards for mounts, working directories, cleanup, and missing Docker/runtime image errors.
5. Document local Docker usage, target names, prerequisites, and platform caveats.
6. Verify representative Docker targets locally and compare behavior with CI target execution.

## Open Questions

- Should Docker mode build and pack automatically by default, or require an explicit tarball path unless `--pack` is passed?
- Should Docker full-matrix execution run targets sequentially for predictable logs or allow opt-in parallel execution?
- Which cache directories, if any, are safe enough to mount for faster repeated runs?
