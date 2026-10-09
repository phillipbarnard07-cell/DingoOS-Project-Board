# Engineering update — 2026-10-10: IP-06

**Workstream:** DingoOS protected-IP governance and provenance integrity  
**State:** Checkpoint/recovery reference protocol pushed; independent production anchor and executable verification pending  
**Private implementation PR:** https://github.com/phillipbarnard07-cell/DingoOS/pull/74  
**Dependency:** IP-05 durable review ledger, PR #73

## Delivered

- Typed checkpoint records with canonical payloads and HMAC-SHA-256 authentication.
- Key identifier validation and constant-time MAC comparison.
- Fail-closed comparison between the local event chain and a previously anchored prefix.
- Detection of shorter restored histories and same-length replacement histories.
- Monotonic in-memory test adapter and atomic local-file demonstration adapter.
- Eight standard-library regression tests authored.
- Formal specification with threat boundaries and recovery acceptance gates.

## Status

| Gate | State |
|---|---|
| Source, tests, specification committed | DONE |
| Draft PR opened | DONE |
| Regression tests authored | DONE |
| Tests executed against exact canonical revision | NOT VERIFIED |
| Independent production CheckpointStore | PENDING |
| Protected external key management | PENDING |
| Canonical provenance/release-manifest integration | PENDING |
| Release authorization | HOLD |

## Important trust boundary

The local-file adapter is a development demonstration, not an independent trust domain. An attacker able to rewrite both the ledger and checkpoint, or obtain the HMAC key, can defeat the intended protection. Production needs independent checkpoint custody, protected keys outside the ledger host, atomic compare-and-publish, audit, retention and recovery controls.

No legal conclusion, production certification, or release authorization is asserted. No paid API or hosted service was introduced.

## Next verification gate

Run IP-06 and the IP-05/IP-04/IP-03 test suites against the exact revision, retain the output and revision digest, then implement an approved independent anchor adapter and test rollback, backup restoration, concurrent checkpoint advancement, key rotation and outage behavior.
