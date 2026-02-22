---
name: pm-hybrid-prioritizer
description: Rank backlog items with a hybrid PM score and deterministic ordering, with V2 route evidence and mutation gating.
---

# PM Hybrid Prioritizer

## Default Formula
`score = customer_pain*0.30 + strategic_impact*0.25 + urgency*0.20 + risk_reduction*0.15 - effort*0.10`

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to resolve ranking/reordering operations.
2. Confirm selected operations align with mutation scope.
3. Require `routing_decisions_v2` + `mutation_gate_v2`.
4. If gate blocks writes, return ranking-only output.

## Workflow
1. Score each story with normalized 0-100 dimensions.
2. Compute score and map to priority bands.
3. Apply deterministic tie ordering.
4. Return ranked backlog with rationale.

## Output Contract (V2)
Return:
- `scoring_weights`
- `scored_items`
- `ranked_order`
- `priority_changes`
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Keep scoring deterministic across runs.
- Document assumptions for missing dimension data.
