---
name: pm-feedback-triage-router
description: Convert validated product feedback into backlog-ready stories mapped to feature/release/priority context with V2 route evidence and mutation gating.
---

# PM Feedback Triage Router

## Use When
- Feedback volume is high and needs consistent backlog conversion.
- Team wants standardized triage into story-map structure.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to resolve feedback/backlog operations.
2. Select create/update operations for feedback mapping.
3. Require `routing_decisions_v2` + `mutation_gate_v2`.
4. If gate blocks writes, output triage map only.

## Workflow
1. Normalize feedback signal and severity.
2. Match to existing feature/epic context.
3. Create/update story records with acceptance criteria.
4. Tag priority/state/release context.

## Output Contract (V2)
Return:
- `feedback_to_story_map`
- `created_items`
- `updated_items`
- `unmapped_feedback`
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Reuse existing stories when intent already exists.
- Avoid duplicate creation for equivalent issues.
