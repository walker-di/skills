---
name: fortalece-api-routing-guide
description: Select the correct Fortalece API operation for a PM intent using deterministic route evidence, runtime auth probes, and strict mutation gating.
---

# Fortalece API Routing Guide

## Use When
- A PM or planning skill needs to choose Fortalece API operations safely.
- The request mixes read/write actions and requires auth/scope verification.
- You must produce mutation-safe evidence before proposing or applying backlog changes.

## Inputs
- User intent text.
- Optional project/workspace context.
- Optional explicit operation or endpoint hints.

## Policy Baseline (V2)
- Canonical story-map family is `/api/story-map/**`.
- Canonical queue family is `/api/queue/**` (`jwt-first`).
- Canonical privileged families are `/api/skills/**` and `/api/ai-tools/**` (`session-only`).
- Legacy admin story-map path `/api/admin/story-map` is invalid for routing decisions.
- If canonical story-map operations cannot be proven, emit a mutation blocker (`allowed_to_mutate=false`).

## Auth + Base URL Defaults
- Default API base URL: `https://fortalece.ai`
- Override with `FORTALECE_BASE_URL` when needed.
- API key source: `FORTALECE_API_KEY`
- Header for jwt-first routes: `x-api-key`
- Workspace resolution for probes must include `x-workspace-id`.

## Safe Bootstrap (No `source`)

```bash
ENV_FILE="apps/fortalece-ai-web/.env"

if [ -z "${FORTALECE_API_KEY:-}" ] && [ -f "$ENV_FILE" ]; then
  FORTALECE_API_KEY="$(awk -F= '/^FORTALECE_API_KEY=/{sub(/^FORTALECE_API_KEY=/, ""); print; exit}' "$ENV_FILE")"
fi

if [ -z "${FORTALECE_API_KEY:-}" ]; then
  echo "Missing FORTALECE_API_KEY" >&2
  exit 1
fi

FORTALECE_BASE_URL="${FORTALECE_BASE_URL:-https://fortalece.ai}"
WORKSPACE_ID="${WORKSPACE_ID:-<workspace-id>}"

curl -sS -D /tmp/fortalece_headers.txt -o /tmp/fortalece_body.json \
  -H "x-api-key: $FORTALECE_API_KEY" \
  -H "x-workspace-id: $WORKSPACE_ID" \
  "$FORTALECE_BASE_URL/api/work-items"

awk 'tolower($1)=="x-fortalece-auth-mode:" {print "auth_mode=" $2}' /tmp/fortalece_headers.txt
```

## Mandatory Workflow
1. Classify intent into one or more action goals.
2. Load `references/intent-overrides.json` and apply first-match rule.
3. Resolve candidate operations from `references/public-api-operation-index.generated.json`.
4. Filter candidates by `references/endpoint-family-matrix.md`.
5. Execute runtime probe for the primary candidate operation:
   - include `x-api-key` and `x-workspace-id`
   - capture observed mode from `x-fortalece-auth-mode`
6. Build `routing_decisions_v2` (`RouteEvidenceV2[]`) with confidence.
7. Build `mutation_gate_v2`:
   - set `allowed_to_mutate=false` if auth/scope mismatch, unresolved operation, or story-map canonical gap.
8. Return output contract before downstream PM planning/mutation steps.

## RouteEvidenceV2 Contract
Each entry must include:
- `intent`
- `operation`
- `endpoint_family`
- `auth_mode_expected`
- `scope_requirements`
- `source_of_truth` (`intent_override` | `operation_index` | `policy_matrix` | `runtime_probe` | `manual_gap`)
- `workspace_resolution` (`header:x-workspace-id` | `query:workspace` | `cookie`)
- `mutation_class`
- `confidence` (`high` | `medium` | `low`)
- `why`

## MutationGateV2 Contract
Return:
- `allowed_to_mutate` (`true` | `false`)
- `blockers` (`string[]`)
- `auth_observed_mode` (`jwt` | `api-key` | `session-fallback` | `unknown`)
- `scope_verified` (`string[]` containing `workspace`, optional `project`)
- `idempotency_key_strategy` (`string`)

## Guardrails
- Treat generated operation index as canonical for non-webhook `/api` scope.
- Exclude `/api/webhooks/**` from decisions.
- Do not invent routes or operations outside the generated index.
- For story-map intents, do not fall back to legacy admin path.
- If operation cannot be proven, return `source_of_truth=manual_gap` with a blocker.

## Output Contract
Return:
- `routing_summary`
- `routing_decisions_v2`
- `mutation_gate_v2`
- `fallback_operations`
- `policy_notes`
- `open_questions`

## References
- `references/decision-rubric.md`
- `references/endpoint-family-matrix.md`
- `references/auth-bootstrap.md`
- `references/intent-overrides.json`
- `references/public-api-operation-index.generated.json`
- `references/public-api-operation-index.generated.md`
