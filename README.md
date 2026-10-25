# ASSIST OS / AssistAI — v6.2 curated source

This repository is being populated from the supplied `ASSIST_OS_v6_2_TESTED_AND_DOCKER_STACK.zip` after source and deployment review. It contains the Foundation API, ASSIST OS backend, self-operating kernel, deployment examples, tests and project documentation.

**Audit status: not production-certified.** Read [`AUDIT_REPORT.md`](AUDIT_REPORT.md) before running. Docker was unavailable in the audit environment, so container builds and live deployment were not verified.

## Main components
- `foundation/` — lightweight AssistAI API and model router.
- `assist_os/backend/` — FastAPI backend, authentication, agents, governance and connectors.
- `assist_os/self_operating_kernel/` — kernel API, worker, monitoring and service files.
- `docker-compose.minimal.yml` — local Compose entry point.
- `AUDIT_REPORT.md` — findings, tests, limitations and next validation gate.

## Security
Do not expose the APIs publicly until authentication, authorization, execution sandboxing, reverse-proxy policy, backups and secret management have been reviewed. Replace all example secret values before running. Keep `.env` out of Git.

## Validation so far
- Foundation tests: 10 passed.
- Self-operating Kernel tests: 10 passed.
- Backend tests in the available environment: 6 passed; 4 async integration tests require `aiosqlite` and were not collected after a test-fixture robustness fix.
- Python syntax check: 156 files, 0 failures.
- Compose YAML parse and referenced-path checks passed. Actual Docker builds/runtime remain unverified.

## License
No project-wide license was present in the supplied archive. Reuse/redistribution permissions are therefore not explicitly granted until the owner adds a license.