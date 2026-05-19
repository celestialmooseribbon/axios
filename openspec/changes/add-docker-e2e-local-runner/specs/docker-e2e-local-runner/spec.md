## ADDED Requirements

### Requirement: Docker-Backed Target Execution
The local e2e tooling SHALL support running package-contract e2e targets inside Docker containers.

#### Scenario: Run one Docker target
- **WHEN** a maintainer requests a single Docker e2e target
- **THEN** the tooling runs only that target in the configured runtime container

#### Scenario: Run target group
- **WHEN** a maintainer requests a Docker e2e target group
- **THEN** the tooling runs each target in that group using its configured runtime container

#### Scenario: Run full Docker matrix
- **WHEN** a maintainer requests the full Docker e2e matrix
- **THEN** the tooling runs all configured Docker-capable e2e targets

### Requirement: Shared E2e Matrix
Docker-backed local execution SHALL use the same target definitions as the package-contract e2e suite where practical.

#### Scenario: Target names match CI concepts
- **WHEN** a Docker e2e target is listed
- **THEN** its name matches the corresponding e2e matrix target or target group used by CI

#### Scenario: Docker metadata extends target definition
- **WHEN** a target supports Docker execution
- **THEN** its Docker image, platform, working directory, and command are defined as target metadata rather than duplicated in ad hoc scripts

### Requirement: Packed Artifact Preservation
Docker-backed e2e execution SHALL test the packed axios artifact, not repository-relative source imports.

#### Scenario: Tarball supplied to Docker target
- **WHEN** a Docker e2e target starts
- **THEN** the packed axios tarball is available inside the container as the package under test

#### Scenario: Missing tarball fails early
- **WHEN** Docker e2e execution cannot find or create the packed axios tarball
- **THEN** the run fails before executing behavior cases

### Requirement: Optional Local Tooling
Docker SHALL be optional for routine development and host-mode e2e runs.

#### Scenario: Host e2e remains available
- **WHEN** Docker local execution is added
- **THEN** existing or planned host-mode e2e execution remains available for quick iteration

#### Scenario: Docker missing on host
- **WHEN** a maintainer invokes Docker e2e without Docker installed or running
- **THEN** the tooling fails with a clear prerequisite message instead of affecting non-Docker test commands

### Requirement: Isolated Container Execution
Docker e2e runs SHALL avoid mounting host credentials, package-manager secrets, and the host Docker socket.

#### Scenario: Minimal mounts
- **WHEN** a Docker e2e target runs
- **THEN** it mounts only the repository, packed artifact, temporary workspace, or explicitly safe cache paths required for the target

#### Scenario: Docker socket is not mounted
- **WHEN** a Docker e2e target runs
- **THEN** the host Docker socket is not mounted into the container

#### Scenario: Credential paths are not mounted
- **WHEN** a Docker e2e target runs
- **THEN** host credential paths such as npm, GitHub, SSH, cloud, or home-directory credential stores are not mounted into the container

### Requirement: Runtime Image Coverage
Docker e2e target metadata SHALL cover selected Node, Bun, Deno, and TypeScript package-contract targets.

#### Scenario: Node Docker targets
- **WHEN** Node Docker targets are configured
- **THEN** they cover the selected CJS and ESM Node versions from the package-contract e2e matrix

#### Scenario: Bun and Deno Docker targets
- **WHEN** Bun and Deno Docker targets are configured
- **THEN** they use runtime containers capable of executing the corresponding package-contract e2e cases

#### Scenario: TypeScript Docker targets
- **WHEN** TypeScript Docker targets are configured
- **THEN** they can compile the selected CJS and ESM consumer fixtures against the packed package declarations

### Requirement: Local Documentation
The e2e documentation SHALL explain Docker-backed local execution, prerequisites, target selection, and known platform caveats.

#### Scenario: Targeted run documentation
- **WHEN** a maintainer reads the e2e documentation
- **THEN** they can identify the command for running one Docker target, one target group, and the full Docker matrix

#### Scenario: Apple Silicon caveat documentation
- **WHEN** documentation describes old Node Docker targets
- **THEN** it notes any platform or performance caveats for Apple Silicon and other non-amd64 hosts

#### Scenario: Security model documentation
- **WHEN** documentation describes Docker e2e execution
- **THEN** it explains the mount and credential restrictions used by the local runner
