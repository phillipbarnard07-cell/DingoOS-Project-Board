# Horizon local mathematics adapter — 2026-10-10

**Status:** Local, free-to-run backend adapter committed; frontend wiring remains the next block.

The adapter wraps the canonical E-HOVER sizing kernel, validates a versioned JSON request, provides structured status/errors and provenance, preserves separate governance states, and binds to loopback only. JSON Schema and contract tests are included. It does not authenticate users, connect to SEARM, expose hardware control, or qualify input evidence. Test cases are authored but not executed in this environment. No paid dependency or billing gate added.

Next: connect Horizon to the loopback API behind an explicit connection indicator, add browser/contract tests, and retain a clear offline demo state.
