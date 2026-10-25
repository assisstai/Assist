# Initial source and deployment audit

**Scope:** supplied `grok-workspace (1).zip`, including its embedded `ASSIST_OS_v6_2_TESTED_AND_DOCKER_STACK.zip` and `docker-compose.minimal.yml`. This report describes a curated source extraction, not a production penetration test.

## Summary

- The outer archive contained a separate app-builder workspace, generated `.vercel/output`, `.grok` workspace instructions/skills, screenshots, and the embedded ASSIST OS project. Generated and workspace-specific files were excluded from the public source tree. Two legacy Omarchy setup scripts were also excluded because their system-changing behavior was not validated for the Ubuntu deployment target.
- The embedded project contains 156 Python files; all compiled successfully with Python 3.13.5 during the audit.
- Foundation tests: **10 passed**.
- Self-operating Kernel tests: **10 passed**.
- Backend tests before the curation fix: **6 passed, 4 setup errors** because `aiosqlite` was not installed in the audit runtime. The original `conftest.py` silently omitted fixtures when imports failed. In the curated tree, async integration tests are guarded on `aiosqlite` availability; current result: **6 passed**, with four async integration tests not collected in this environment. Install all declared requirements to run full backend coverage.
- Compose YAML parsing and source-level referenced-path checks passed. Docker is not installed in the audit runtime, so image builds, service startup, health checks, network reachability, resource use and end-to-end behavior were not verified.

## Findings

### High — supplied Compose file was incomplete

The outer `docker-compose.minimal.yml` referenced paths absent from the archive: `foundation/Dockerfile`, `assist_os/backend/Dockerfile`, and `assist_os/backend/db/init-databases.sql`. These were added to the curated tree to resolve the paths. This is a source-level repair only; real Docker build/run validation remains required.

### High — predictable default credentials

The supplied Compose file used predictable fallback values for PostgreSQL, JWT, vault, kernel, UI and webhook secrets. The curated Compose file requires explicit secret interpolation and includes `.env.minimal.example`. Replace all sample values before starting, keep `.env` out of Git, and do not expose the stack publicly based on this audit alone.

### Medium — SQLAlchemy model argument warning

`assist_os/backend/src/github/models.py` passed `foreign_keys=` to `mapped_column`, which SQLAlchemy warned is not a valid dialect option. The invalid argument was removed; the explicit relationship join is retained. Database behavior still needs PostgreSQL and SQLite integration testing.

### Medium — test fixture handling

The original backend test setup caught `ImportError` broadly and silently omitted async fixtures. If a declared test dependency is missing, tests failed later with a misleading missing-fixture error. The curated test module guards async integration tests on `aiosqlite`; install backend requirements to run them.

### Medium — Ollama profile URL

The Compose file defaults to host Ollama via `host.docker.internal`. If the optional Ollama profile is enabled, set `OLLAMA_HOST=http://ollama:11434` and verify connectivity from each service.

### Medium — remote-control and execution features

The source includes terminal/subprocess execution, browser automation, tool execution and admin APIs. This audit did not prove that every execution route is isolated against untrusted input. Keep access loopback-only, use unique secrets, and do not enable remote access until command policy, authorization, audit logging, sandboxing and reverse-proxy behavior have been reviewed.

### Low — no project-wide license

The supplied project did not include a root license file. No license has been invented; add the license intended by the owner before inviting external contributions or redistribution.

## Secret scan

A pattern scan of the included source found no obvious live API-token or private-key material. Provider keys in the included provider template are blank placeholders. The project does contain intentional development/test passwords and predictable default secret strings in source/config examples; never reuse them in production. This was a pattern scan, not a guarantee that every secret format was detected.

## Not verified

- Docker image builds or the complete Compose deployment.
- Runtime behavior on Ubuntu or memory use on a 16 GB machine.
- Dependency vulnerability status or a full SBOM review.
- All authentication, authorization, tenant isolation, SSRF, shell-execution, browser sandbox and secrets-vault paths.
- Remote-control operation from Android or integration with Open WebUI.

## Next validation gate

Run `docker compose -f docker-compose.minimal.yml config`, build the images, start the stack on clean Ubuntu with generated secrets, verify every health endpoint and intended API route, run all tests with declared dependencies installed, and inspect logs for missing routers or migrations before treating the release as deployable.
