---
name: pm-roadmap-status-reporter
description: Produce stakeholder-ready roadmap updates with progress and risk visibility, including V2 route evidence and mutation gating context.
---

# PM Roadmap Status Reporter

## Use When
- Weekly/biweekly stakeholder update is needed.
- Leadership needs concise progress and risk visibility.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to choose reporting operations.
2. Select read operations for roadmap evidence.
3. Require `routing_decisions_v2` + `mutation_gate_v2`.
4. If mutation gate is blocked, still proceed (reporting is read-only) and note blockers.

## Workflow
1. Summarize story/release progression.
2. Highlight delayed, blocked, and high-risk items.
3. Compare movement since last snapshot.
4. Produce concise decision-oriented update.

## Output Contract (V2)
Return:
- `status_snapshot`
- `progress_metrics`
- `risks_and_blockers`
- `changes_since_last_update`
- `next_focus`
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Keep language executive-readable.
- Do not mutate backlog data.
