## 1. Matrix And Runner Integration

- [ ] 1.1 Extend the e2e target matrix with Docker metadata for Node, Bun, Deno, and TypeScript targets.
- [ ] 1.2 Add Docker target selection support for one target, a target group, and the full matrix.
- [ ] 1.3 Ensure Docker mode invokes the same package-contract e2e harness used by host-mode and CI execution.
- [ ] 1.4 Make Docker mode consume an existing packed axios tarball or produce one through an explicit documented option.
- [ ] 1.5 Add clear error handling for missing Docker, unavailable images, unsupported target names, and missing tarballs.

## 2. Container Execution Model

- [ ] 2.1 Configure Node Docker targets for the selected CJS and ESM Node versions from the e2e matrix.
- [ ] 2.2 Configure Bun Docker targets for Bun package-contract cases.
- [ ] 2.3 Configure Deno Docker targets for Deno package-contract cases.
- [ ] 2.4 Configure TypeScript Docker targets for CJS and ESM declaration compatibility cases.
- [ ] 2.5 Add platform handling for old Node images where non-amd64 hosts may need explicit Docker platform selection.
- [ ] 2.6 Ensure per-target temporary workspaces are isolated and cleaned up or left only in an explicitly requested debug mode.

## 3. Security And Isolation

- [ ] 3.1 Restrict Docker mounts to the repository, packed artifact, temporary workspace, or explicitly safe cache paths required by the target.
- [ ] 3.2 Ensure Docker mode does not mount the host Docker socket.
- [ ] 3.3 Ensure Docker mode does not mount host npm, GitHub, SSH, cloud, or home-directory credential paths.
- [ ] 3.4 Preserve install-script safeguards for package installation inside Docker targets where package-manager installs occur.
- [ ] 3.5 Document the local Docker security model and any intentional cache mounts.

## 4. Local Commands And Documentation

- [ ] 4.1 Add package scripts for targeted Docker e2e execution and full Docker matrix execution.
- [ ] 4.2 Document Docker prerequisites and the relationship between host-mode, Docker-mode, and CI e2e execution.
- [ ] 4.3 Document examples for running one target, one target group, and the full Docker matrix.
- [ ] 4.4 Document Apple Silicon and other non-amd64 caveats for legacy runtime images.
- [ ] 4.5 Document troubleshooting for missing Docker daemon, image pulls, tarball path errors, and cleanup/debug modes.

## 5. Verification

- [ ] 5.1 Run a Docker-backed Node CJS e2e target against a freshly packed axios tarball.
- [ ] 5.2 Run a Docker-backed Node ESM e2e target against a freshly packed axios tarball.
- [ ] 5.3 Run Docker-backed Bun and Deno e2e targets where local Docker images are available.
- [ ] 5.4 Run Docker-backed TypeScript e2e targets for CJS and ESM declaration compatibility.
- [ ] 5.5 Run `npm run lint`.
- [ ] 5.6 Run `npm run build` and `npm pack` before Docker e2e verification.
- [ ] 5.7 Compare representative Docker local target results with the corresponding CI e2e target behavior.
