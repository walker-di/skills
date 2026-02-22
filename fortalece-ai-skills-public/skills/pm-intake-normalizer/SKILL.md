---
name: pm-intake-normalizer
description: Normalize vague product requests into a strict V2 PM brief with explicit assumptions, non-goals, route evidence, and mutation gating.
---

# PM Intake Normalizer

## Use When
- A request is vague, overloaded, or missing acceptance boundaries.
- Backlog intent exists but mutation safety is unclear.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to classify intent.
2. Resolve candidate operations and endpoint families.
3. Require `routing_decisions_v2` and `mutation_gate_v2` before drafting mutation paths.
4. If `allowed_to_mutate=false`, keep output planning-only and highlight blockers.

## Workflow
1. Restate request as one product objective.
2. Extract/infer primary personas.
3. Capture constraints (timeline, risk, quality, compliance, scope boundaries).
4. Define measurable success metrics.
5. Produce explicit non-goals.
6. Record assumptions with confidence.
7. Attach route evidence and mutation gate.

## Output Contract (V2, Required)
Return:
- `objective`
- `personas`
- `constraints`
- `success_metrics`
- `scope_in`
- `scope_out`
- `non_goals`
- `assumptions` (item, confidence, rationale)
- `open_questions`
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Do not mutate backlog items in this skill.
- Do not invent business constraints; label assumptions explicitly.
- Missing required V2 fields invalidates output.
