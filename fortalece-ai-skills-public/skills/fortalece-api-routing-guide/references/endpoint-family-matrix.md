# Endpoint Family Matrix (Fortalece, V2)

## `jwt-first` families
- `/api/projects/**`
- `/api/feedback/**`
- `/api/work-items/**`
- `/api/story-map/**`
- `/api/queue/**`
- `/api/blog/**`
- `/api/marketing/**`
- `/api/integrations/**`
- `/api/issues/**`
- `/api/agents/**`
- `/api/agent/**`
- `/api/notifications`
- `/api/observability/**`

## `session-only` families
- `/api/skills/**`
- `/api/ai-tools/**`
- `/api/workspace/members/**`
- `/api/workspace/invitations/**`
- `/api/workspace/transfer-ownership`
- `/api/admin/site-roles/**`
- `/api/billing/**`

## `public` families
- `/api/embeds/**`

## Issuer Rule
- `/api/auth/jwt` remains session-authenticated issuer endpoint.

## Scope Notes
- `workspace` is baseline scope for all non-public PM workflows.
- `project` is required for project-partitioned backlog workflows.

## Story-Map Policy Note
- Canonical story-map family is `/api/story-map/**`.
- Legacy admin story-map path `/api/admin/story-map` is not valid for V2 routing decisions.
