# Auth Bootstrap (V2)

## Defaults
- `FORTALECE_BASE_URL` default: `https://fortalece.ai`
- API key env var: `FORTALECE_API_KEY`
- Auth header: `x-api-key: <key>`
- Workspace probe header: `x-workspace-id: <workspace-id>`

## Portable Bootstrap (Safe)

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

## Notes
- Do not `source apps/fortalece-ai-web/.env`; JSON-like env values can break shell parsing.
- For local development, use `FORTALECE_BASE_URL=http://localhost:5173`.
- `session-only` endpoints still require a session cookie.
