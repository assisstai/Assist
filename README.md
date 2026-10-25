# ASSIST OS / AssistAI — v6.2 audit staging

This repository currently contains the audit documentation and is being prepared for the curated source import. The full reviewed source bundle is available in the conversation as `ASSIST_OS_v6_2_GitHub_Ready_Audited.zip`; it has **not yet been committed here as individual source files**.

**Audit status: not production-certified.** Read [`AUDIT_REPORT.md`](AUDIT_REPORT.md) before running. Docker was unavailable in the audit environment, so container builds and live deployment were not verified.

## Expected source layout in the prepared bundle
- `foundation/` — lightweight AssistAI API and model router.
- `assist_os/backend/` — FastAPI backend, authentication, agents, governance and connectors.
- `assist_os/self_operating_kernel/` — kernel API, worker, monitoring and service files.
- `docker-compose.minimal.yml` — local Compose entry point.

## Validation so far
- Foundation tests: 10 passed.
- Self-operating Kernel tests: 10 passed.
- Backend tests in the available environment: 6 passed; 4 async integration tests require `aiosqlite` and were not collected after a test-fixture robustness fix.
- Python syntax check: 156 files, 0 failures.
- Compose YAML parse and referenced-path checks passed. Actual Docker builds/runtime remain unverified.

## Security
Do not expose APIs publicly until authentication, authorization, execution sandboxing, reverse-proxy policy, backups and secret management have been reviewed. Replace sample secrets before running. Keep `.env` out of Git.

## License
No project-wide license was present in the supplied archive. Reuse/redistribution permissions are therefore not explicitly granted until the owner adds a license.