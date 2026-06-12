# Noah Debate Assistant

A Lincoln-Douglas debate coach app that challenges every argument until it's airtight.

## Run & Operate

- `pnpm --filter @workspace/api-server run dev` — run the API server (port 5000)
- `pnpm --filter @workspace/debate-app run dev` — run the frontend (port auto-assigned)
- `pnpm run typecheck` — full typecheck across all packages
- `pnpm run build` — typecheck + build all packages
- `pnpm --filter @workspace/api-spec run codegen` — regenerate API hooks and Zod schemas from the OpenAPI spec
- `pnpm --filter @workspace/db run push` — push DB schema changes (dev only)
- Required env: `DATABASE_URL` — Postgres connection string
- Required env: `OPENAI_API_KEY` — OpenAI API key for Noah's responses

## Stack

- pnpm workspaces, Node.js 24, TypeScript 5.9
- Frontend: React + Vite + Tailwind CSS + shadcn/ui
- API: Express 5
- DB: PostgreSQL + Drizzle ORM
- AI: OpenAI gpt-4o via direct API key
- Validation: Zod (`zod/v4`), `drizzle-zod`
- API codegen: Orval (from OpenAPI spec)
- Build: esbuild (CJS bundle)

## Where things live

- `lib/api-spec/openapi.yaml` — OpenAPI spec (source of truth for all API contracts)
- `lib/db/src/schema/` — Drizzle ORM schema (conversations.ts, messages.ts)
- `lib/integrations-openai-ai-server/` — OpenAI server client
- `artifacts/api-server/src/routes/openai/` — Chat API routes
- `artifacts/debate-app/src/` — React frontend

## Architecture decisions

- SSE streaming used for chat responses (token-by-token) — Orval hooks don't support streaming, so raw fetch is used for the send-message endpoint
- Noah's system prompt is baked into the server route (from the original config.json)
- Conversations and messages are persisted in PostgreSQL for history
- gpt-4o used for higher quality debate reasoning (original was gpt-3.5-turbo)

## Product

- Start a new debate session by naming a resolution (e.g., "Capital punishment is morally justified")
- Noah challenges arguments step-by-step in Lincoln-Douglas style
- Past sessions are saved in the sidebar for review
- Real-time streaming responses give a live "thinking" feel

## User preferences

_Populate as you build — explicit user instructions worth remembering across sessions._

## Gotchas

- DB schema exports `conversations` and `messages` (not `conversationsTable`/`messagesTable`) — the route file aliases them on import
- SSE streaming endpoint (`POST /api/openai/conversations/:id/messages`) must be called with raw fetch, not the generated hook
- Always run `pnpm --filter @workspace/api-spec run codegen` after any spec changes

## Pointers

- See the `pnpm-workspace` skill for workspace structure, TypeScript setup, and package details
