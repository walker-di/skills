---
name: pm-backlog-drift-monitor
description: Monitor backlog drift against prioritization/dependency policy and apply corrective alignment with V2 route evidence and mutation gating.
---

# PM Backlog Drift Monitor

## Use When
- Backlog quality degrades after prioritization.
- Teams need continuous policy alignment.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to resolve drift-read and correction-write operations.
2. Select deterministic operations for detection/correction.
3. Require `routing_decisions_v2` + `mutation_gate_v2`.
4. If gate is blocked, output correction plan only.

## Workflow
1. Compare current backlog against policy baseline.
2. Detect priority, dependency, and stale-state drift.
3. Propose/apply corrective updates.
4. Report drift trends and remaining variance.

## Output Contract (V2)
Return:
- `drift_summary`
- `drift_findings`
- `corrections_applied`
- `remaining_drift`
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Prefer reversible, low-risk corrections first.
- Preserve deterministic ordering rules.
