# Horizon local mathematics adapter — integration update

**Status:** Local backend adapter and same-origin Horizon wiring committed; runtime verification remains open.

- Local standard-library Python server serves Horizon at `http://127.0.0.1:8765/` and the API from the same origin.
- Calculator submits versioned requests to `POST /api/v1/mathematics/screening`, which calls the canonical sizing kernel.
- Backend rejection is surfaced rather than silently replaced by browser output; browser fallback is labelled and used only when the API cannot be reached.
- Request schema, structured errors, provenance, separated governance states and authored contract tests are included.
- No test execution or browser smoke test is claimed. Service has no authentication and is local development only; do not expose it publicly.
- No paid dependencies or billing gate introduced.

Next: execute free local contract tests, smoke-test the browser flow, add response schema validation and security review before any deployment.
