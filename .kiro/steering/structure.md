# Project Structure

Three top-level packages plus deploy helpers and docs at the repo root.

```
frontend/        React + Vite + TypeScript SPA
backend/         Node.js AWS Lambda functions
infrastructure/  AWS CDK app (provisions all cloud resources)
DESIGN_LANGUAGE.md
deploy-*.ps1 / deploy-*.sh
```

## frontend/src
- `pages/` — route-level screens (Login, Register, Feed, CreatePost, Profile)
- `components/` — reusable UI (e.g. ApiUrlDisplay, ErrorBoundary)
- `contexts/` — React context providers (AuthContext)
- `services/` — API clients (`api.ts`, grouped as `authApi`, `usersApi`, `postsApi`)
- `types/` — shared TS types (`user.ts`, `post.ts`)
- `App.tsx` / `main.tsx` — app root and router; `App.css` / `index.css` — styles

## backend/src
- `common/` — shared code; `middleware.js` exports the `withAuth` wrapper
- `functions/` — one folder per domain, each file is a single Lambda handler:
  - `auth/` — login, register
  - `posts/` — createPost, getPosts, likePost (and related)
  - `users/` — getProfile, updateProfile, followUser, unfollowUser, checkFollowing
  - `monitoring/` — emitCustomMetrics
- `dist/` — build output and packaged Lambda zips (generated)

## infrastructure
- `bin/` — CDK app entrypoint
- `lib/app-stack.ts` — the single `AppStack` definition
- `cdk.json`, `cdk.out/` — CDK config and synthesized output

## Conventions
- Backend handlers are CommonJS (`require`/`module.exports`) authored in `.js`.
- Protected endpoints wrap the handler with `withAuth` and read the user from `event.user` (`{ id, username }`).
- Every Lambda response includes JSON body plus CORS headers (`Access-Control-Allow-Origin: *`, `Access-Control-Allow-Credentials: true`); return proper status codes (400 for validation, 401 for auth, 500 for errors).
- Table names come from environment variables (e.g. `USERS_TABLE`, `POSTS_TABLE`); never hard-code them.
- DynamoDB access uses the v3 `DynamoDBDocumentClient`; clients are initialized once at module scope and reused.
- Frontend API calls go through `src/services/api.ts`; add new endpoints there rather than calling `fetch` directly in components. Pass the auth token as `Authorization: Bearer <token>`.
- One Lambda handler per file, grouped by domain folder.
