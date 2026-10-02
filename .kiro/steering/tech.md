# Tech Stack

A serverless full-stack application split into three packages: `frontend`, `backend`, and `infrastructure`.

## Frontend
- React 18 + TypeScript, built with Vite
- Routing: react-router-dom v6
- State/auth: React Context (`src/contexts/AuthContext.tsx`)
- API access: `fetch` wrappers in `src/services/api.ts`; base URL comes from `VITE_API_URL` env var
- Plain CSS with CSS custom properties (no CSS framework); styles in `App.css` / `index.css`
- E2E tests: Playwright

## Backend
- Node.js AWS Lambda functions (CommonJS, `.js`)
- AWS SDK v3: `@aws-sdk/client-dynamodb`, `@aws-sdk/lib-dynamodb`, `@aws-sdk/client-cognito-identity-provider`
- `uuid` for IDs
- Auth via shared `withAuth` middleware in `src/common/middleware.js`
- Build step copies `src/*` to `dist/` (TypeScript config exists but functions are authored in JS)

## Infrastructure
- AWS CDK v2 (TypeScript), stack in `infrastructure/lib/app-stack.ts`
- Provisions: Cognito User Pool + Identity Pool, DynamoDB tables (users, posts, likes, comments, follows), API Gateway REST API, Lambda functions, S3 + CloudFront for frontend hosting
- Lambda code is deployed from prebuilt zip packages under `backend/dist/lambda-packages`

## Common Commands

### Frontend (`cd frontend`)
- `npm run dev` — start Vite dev server (run manually; long-running)
- `npm run build` — type-check (`tsc`) and build
- `npm run lint` — ESLint (max-warnings 0)
- `npm run test:e2e` — Playwright E2E tests

### Backend (`cd backend`)
- `npm run build` — copy `src` to `dist`
- `npm run deploy` / `npm run deploy-with-deps` — zip Lambda deployment package
- Lambda packaging scripts: `scripts/prepare-lambda-packages.ps1` (Windows) / `.sh`

### Infrastructure (`cd infrastructure`)
- `npm run build` — compile CDK (`tsc`)
- `npm run diff` — `cdk diff`
- `npm run deploy` — `cdk deploy`

### Root helpers
- `deploy-backend.ps1` / `deploy-backend.sh`, `deploy-frontend.ps1`

## Notes
- Environment is Windows with cmd shell. Build/deploy scripts exist in both PowerShell (`.ps1`) and bash (`.sh`); prefer the `.ps1` variants locally.
- Deploying to AWS (CDK deploy, Lambda uploads) affects live cloud resources — confirm before running.
