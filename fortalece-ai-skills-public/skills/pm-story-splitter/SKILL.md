---
name: pm-story-splitter
description: Decompose large epics/features into thin vertical stories with strict V2 contracts, dependency DAG validation, and mutation payload blueprints.
---

# PM Story Splitter

## Use When
- Features are too large for predictable delivery.
- Stories are broad, vague, or not testable.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to map split intent to operations.
2. Select create/update operations for target family.
3. Require `routing_decisions_v2` + `mutation_gate_v2`.
4. If gate is blocked, output split only and mark mutation blueprint as blocked.

## Workflow
1. Identify user outcome and split by independent value slices.
2. Write explicit acceptance criteria for each slice.
3. Write detailed story descriptions for each slice (`Context`, `Detailed scope`, `Definition of done`).
4. If requested, derive a task detail pack per story (titles, descriptions, acceptance criteria).
5. Build dependency DAG and verify acyclic structure.
6. Define mutation payload blueprints for create/update paths (including `description` fields).
7. Derive deterministic idempotency keys per story.

## Output Contract (V2, Required)
Return:
- `parent_scope`
- `proposed_stories` (id, story, value_slice, idempotency_key)
- `story_detail_map` (story_id -> detailed description)
- `task_detail_pack` (optional; story_id -> tasks[])
- `acceptance_criteria_map`
- `dependency_dag` (nodes, edges, has_cycle)
- `mutation_payload_blueprint` (create, update)
- `mutation_plan` (create/update sequence)
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Preserve traceability to original epic/feature.
- Avoid technical tasks with no user outcome.
- Do not treat acceptance criteria as a substitute for detailed descriptions.
- Dependency DAG must be acyclic; otherwise set mutation gate blocker.
- Missing required V2 fields invalidates output.
