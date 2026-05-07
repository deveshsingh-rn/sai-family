# Sai Family Backend Process

This file is the working memory for the backend build. Keep it updated after each completed part so the project can continue smoothly even if the chat context becomes short.

## Objective

Create a Docker-first Node.js backend for the Sai Family mobile app.

The backend must provide:
- Express REST API
- Mobile OTP authentication
- JWT access and refresh tokens
- Role-based access control
- Prisma ORM
- PostgreSQL with PostGIS
- Redis for cache, rate limits, and queues
- BullMQ workers later for notifications and media jobs
- Stable API contracts for the Expo frontend

## Current Backend Status

Created and verified initial backend foundation:
- `backend-api` Node.js TypeScript backend repo
- Express app bootstrap
- Health routes
- Mock OTP routes
- Prisma schema with first core models
- Dockerfile for API
- Backend `docker-compose.yml` for API, Postgres/PostGIS, Redis, and Prisma Studio
- Root `.env.example`
- API `.env.example`
- Basic Vitest health test
- Local `.env` files for development are present and gitignored
- Docker API, Postgres/PostGIS, and Redis containers start successfully
- Prisma initial migration has been applied
- Seed script creates the local super admin user

Verified on May 7, 2026:
```bash
npm run prisma:generate
npm run build
npm test
docker compose config
docker compose up -d db redis
docker compose up -d api
docker compose exec api wget -qO- http://localhost:4000/health
docker compose exec api wget -qO- http://localhost:4000/health/db
docker compose exec api npm run prisma:migrate -- --name init
docker compose exec api npm run seed
docker compose exec api wget -qO- --header='Content-Type: application/json' --post-data='{"mobileNumber":"+910000000000"}' http://localhost:4000/auth/send-otp
```

Known local notes:
- On Apple Silicon, `postgis/postgis:16-3.4` may show an amd64 platform warning, but it started successfully.
- API container needs OpenSSL for Prisma native engine compatibility; this is handled in `backend-api/Dockerfile`.
- Host `curl http://localhost:4000/health` was blocked by the sandbox, but in-container checks passed.

## Backend Build Order

### Part 1: Docker Foundation

Target:
- Backend starts through Docker Compose.
- API returns `GET /health`.
- Database check returns `GET /health/db`.

Steps:
1. Copy `.env.example` to `.env`.
2. Run `docker compose up api db redis`.
3. In another terminal, run Prisma migration inside the API container.
4. Verify:
   - `GET http://localhost:4000/health`
   - `GET http://localhost:4000/health/db`

Debug commands:
```bash
docker compose logs api
docker compose logs db
docker compose logs redis
docker compose exec api npm run prisma:migrate
docker compose exec api npm run seed
```

### Part 2: Prisma Core Schema

Target:
- Core auth and user tables are stable.
- Local seed creates a super admin.

Models started:
- `User`
- `Profile`
- `OtpCode`
- `RefreshToken`
- `AuditLog`
- `Experience`

Next models to add:
- `Event`
- `Business`
- `SevaRequest`
- `Bhajan`
- `Group`
- `PilgrimagePlace`
- `Donation`
- `NotificationPreference`
- `MediaAsset`

### Part 3: Real OTP Authentication

Target:
- `POST /auth/send-otp`
- `POST /auth/verify-otp`
- Real OTP hash stored in DB.
- Redis rate limit by mobile number and IP.
- JWT access and refresh tokens issued after verify.

Current state:
- Mock routes exist.
- They validate request body and return placeholder responses.

Next implementation files:
- `src/services/otp.service.ts`
- `src/services/token.service.ts`
- `src/services/user.service.ts`
- `src/middleware/auth.ts`
- `src/middleware/require-role.ts`

### Part 4: First Feature Slice

Start with experiences feed because it proves the full backend pattern.

Target routes:
- `GET /experiences`
- `POST /experiences`
- `GET /experiences/:id`
- `PATCH /experiences/:id/moderation`

Every module should include:
- route
- controller
- service
- Zod request schemas
- Prisma queries
- tests

### Part 5: Frontend Handoff

Do this only after the backend contracts are stable.

Handoff artifacts:
- endpoint list
- request and response examples
- auth token behavior
- error response format
- DTO/Zod schema location

Frontend starts after:
- health routes pass
- auth routes pass
- first feature API has tests
- Docker flow is repeatable

## Continuity Rule For Future Sessions

If the chat context becomes short or a token limit is reached, continue from this file first.

Before coding, read:
1. `docs/backend-process.md`
2. `blueprint.html`
3. `backend-api/package.json`
4. `backend-api/prisma/schema.prisma`
5. `mobile-app/package.json` if the change touches frontend/mobile integration
6. latest `git status --short`

Then follow this rule:
- If Part 1 is not running in Docker, fix Part 1 first.
- If Docker works, continue Part 2 schema.
- If schema works, continue Part 3 real auth.
- Do not start frontend work until Part 5 handoff criteria are met.

## Repository Ownership Layout

The project is separated by repository ownership:
- `mobile-app` is the Expo React Native app repo.
- `backend-api` is the Node.js Express backend repo.
- the parent folder contains planning docs only.

This keeps mobile builds, backend Docker/deploy work, and future team ownership from blocking each other.

## Current Next Step

Part 1 is complete. Continue with Part 3: replace mock OTP behavior with real OTP and token services.

Immediate target:
- `src/services/otp.service.ts`
- `src/services/token.service.ts`
- `src/services/user.service.ts`
- `src/middleware/auth.ts`
- `src/middleware/require-role.ts`
- Update `src/routes/auth.routes.ts` to call services instead of returning mock tokens.
