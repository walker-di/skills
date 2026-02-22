---
name: pm-release-capacity-planner
description: Allocate prioritized stories into releases/sprints using capacity constraints with V2 route evidence and mutation gating.
---

# PM Release Capacity Planner

## Use When
- Backlog needs release packaging.
- Team needs realistic commitment per release window.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to resolve release/backlog operations.
2. Select read/write operations for assignment and capacity planning.
3. Require `routing_decisions_v2` + `mutation_gate_v2`.
4. If gate blocks writes, return assignment recommendation only.

## Workflow
1. Ingest prioritized backlog and release constraints.
2. Calculate per-release load using story points/capacity.
3. Assign stories with risk-aware balancing.
4. Emit release plan and overflow queue.

## Output Contract (V2)
Return:
- `release_plan`
- `capacity_usage`
- `overflow_items`
- `applied_release_assignments`
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Do not exceed configured release capacity.
- Keep critical blockers visible in plan notes.
