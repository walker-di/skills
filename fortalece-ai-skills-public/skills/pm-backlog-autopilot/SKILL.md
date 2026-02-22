---
name: pm-backlog-autopilot
description: Execute deterministic backlog organization in strict V2 batches with dry-run first, rollback notes, and mutation gating.
---

# PM Backlog Autopilot

## Use When
- User requests automated backlog organization with minimal manual intervention.
- Backlog needs coordinated multi-stage mutation with safety constraints.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` for ordered stage operations.
2. Require `routing_decisions_v2` + `mutation_gate_v2` before any write stage.
3. If `allowed_to_mutate=false`, produce dry-run-only output.

## Workflow
1. Snapshot current backlog state.
2. Build deterministic dry-run preview (no writes).
3. Run content-quality preflight:
   - ensure target stories/tasks include detailed `description` content (not only acceptance criteria)
   - ensure acceptance criteria + description pairs are present for each target mutation
4. Execute bounded mutation batches with explicit failure policy.
5. Apply dependency/priority/release normalization.
6. Run post-write verification and summarize residual risk.

## Output Contract (V2, Required)
Return:
- `execution_summary`
- `dry_run_plan`
- `content_quality_checks` (description + acceptance readiness before writes)
- `mutation_batches` (batch_size, ordering, failure_policy, rollback_note)
- `applied_changes` (must include `route_evidence_id`)
- `failed_changes` (must include `route_evidence_id`)
- `post_run_verification`
- `idempotency_notes`
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Execute in bounded batches.
- Keep operations idempotent and deterministic.
- Fail fast on scope violations.
- Fail fast when description quality gates are not met.
- Missing required V2 fields invalidates output.
