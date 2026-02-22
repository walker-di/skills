---
name: pm-dependency-risk-mapper
description: Identify dependency/blocker chains, quantify risk clusters, and produce dependency update plans with V2 route evidence and mutation gating.
---

# PM Dependency Risk Mapper

## Use When
- Teams report blocked work or unstable sequencing.
- Planning needs explicit dependency visibility.

## API Selection Step (Mandatory First Step)
1. Use `$fortalece-api-routing-guide` to resolve dependency read/write operations.
2. Select in-scope operations for dependency updates.
3. Require `routing_decisions_v2` + `mutation_gate_v2`.
4. If gate blocks writes, output dependency patch plan only.

## Workflow
1. Build dependency graph from backlog links.
2. Detect cycles, bottlenecks, and blocker clusters.
3. Propose/apply `depends_on` and `blocked_by` updates.
4. Return risk-ranked dependency map.

## Output Contract (V2)
Return:
- `dependency_graph_summary`
- `risk_clusters`
- `integrity_issues`
- `applied_dependency_updates`
- `route_evidence_v2` (`RouteEvidenceV2[]`)
- `mutation_gate_v2` (`MutationGateV2`)

## Guardrails
- Validate referenced items exist and are in scope.
- Prevent self-dependency and duplicate links.
