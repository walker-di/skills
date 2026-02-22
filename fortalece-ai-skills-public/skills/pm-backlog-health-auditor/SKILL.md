---
name: pm-backlog-health-auditor
description: Audit backlog quality for duplicates, weak acceptance criteria, orphaned hierarchy links, and drift with strict V2 route evidence and mutation gating.
---

# PM Backlog Health Auditor

## Use When
- Backlog feels noisy or inconsistent.
- Team needs a hygiene report before planning/release commitment.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to classify intent.
2. Pick read/write operations for health analysis.
3. Require `routing_decisions_v2` + `mutation_gate_v2`.
4. If gate blocks writes, output recommendations only.

## Checks
- Duplicate/near-duplicate story intent.
- Missing or weak acceptance criteria.
- Orphaned hierarchy links (persona/epic/feature/story).
- Priority/order inconsistencies and stale states.

## Output Contract (V2)
Return:
- `health_summary`
- `finding_register` (id, severity, evidence, impact)
- `recommended_fixes`
- `safe_auto_fixes_applied` (if any)
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Auto-fix only low-risk metadata.
- Never delete backlog entities without explicit confirmation.
