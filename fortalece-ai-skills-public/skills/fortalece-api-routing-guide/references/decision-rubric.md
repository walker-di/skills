# Decision Rubric (V2)

## Canonical Interfaces

### `RouteEvidenceV2`
| field | type | required | notes |
| --- | --- | --- | --- |
| `intent` | `string` | yes | Normalized action intent key. |
| `operation` | `string` | yes | `METHOD /path` or `UNRESOLVED` for `manual_gap`. |
| `endpoint_family` | `string` | yes | Family form like `/api/work-items/**`. |
| `auth_mode_expected` | `jwt-first \| session-only \| public` | yes | Expected policy mode. |
| `scope_requirements` | `string[]` | yes | Include `workspace`; include `project` when needed. |
| `source_of_truth` | `intent_override \| operation_index \| policy_matrix \| runtime_probe \| manual_gap` | yes | Provenance of route decision. |
| `workspace_resolution` | `header:x-workspace-id \| query:workspace \| cookie` | yes | How workspace scope was resolved. |
| `mutation_class` | `read \| write` | yes | `read` for GET/HEAD, `write` otherwise. |
| `confidence` | `high \| medium \| low` | yes | Decision confidence based on evidence quality. |
| `why` | `string` | yes | Rationale grounded in policy + evidence. |

### `MutationGateV2`
| field | type | required | notes |
| --- | --- | --- | --- |
| `allowed_to_mutate` | `boolean` | yes | Must be `false` if unresolved route/auth/scope risk exists. |
| `blockers` | `string[]` | yes | Empty array when unblocked. |
| `auth_observed_mode` | `jwt \| api-key \| session-fallback \| unknown` | yes | Derived from runtime probe (`x-fortalece-auth-mode`). |
| `scope_verified` | `string[]` | yes | Must include `workspace`; include `project` if verified. |
| `idempotency_key_strategy` | `string` | yes | Deterministic key strategy for mutation safety. |

### `OperationIndexEntry`
| field | type | required | notes |
| --- | --- | --- | --- |
| `operation` | `string` | yes | `METHOD /path`. |
| `method` | `string` | yes | Uppercase HTTP verb. |
| `path` | `string` | yes | OpenAPI path. |
| `endpoint_family` | `string` | yes | Policy family label. |
| `auth_mode` | `jwt-first \| session-only \| public` | yes | From OpenAPI auth metadata. |
| `scope_requirements` | `string[]` | yes | Scope dimensions for validation. |
| `mutation_class` | `read \| write` | yes | Derived from method. |

### `IntentOverrideRule`
| field | type | required | notes |
| --- | --- | --- | --- |
| `id` | `string` | yes | Stable rule ID. |
| `intent` | `string` | yes | Canonical intent key. |
| `match_any` | `string[]` | yes | Keyword triggers. |
| `endpoint_family` | `string` | yes | Preferred family for matched intent. |
| `preferred_operations` | `string[]` | yes | Ordered operation candidates. |
| `scope_requirements` | `string[]` | yes | Required scope checks. |
| `mutation_class` | `read \| write \| mixed` | yes | Intent-level mutation profile. |
| `why` | `string` | yes | Policy rationale. |

## Deterministic Routing Rules
1. Apply override rules in order; first match wins.
2. Validate selected family against endpoint-family matrix.
3. For story-map intent, only `/api/story-map/**` is valid.
4. Resolve operations from generated non-webhook index; do not invent routes.
5. Run runtime probe for primary mutation candidate and record observed auth mode.
6. If route/auth/scope cannot be proven, output `operation=UNRESOLVED`, `source_of_truth=manual_gap`, and block mutations.
7. Always emit both `routing_decisions_v2` and `mutation_gate_v2` before downstream PM actions.
