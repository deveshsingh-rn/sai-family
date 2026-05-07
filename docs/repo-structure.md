# Sai Family Repository Split

The project is intentionally split into two independent repository roots.

```text
sai-family/
├── mobile-app/   # Expo React Native app repo
├── backend-api/  # Node.js Express backend repo
├── docs/         # planning, backend flow, continuity notes
└── blueprint.html
```

## Why This Split Matters

- Mobile builds run from `mobile-app`, so Expo, EAS, app assets, and native config stay isolated.
- Backend development runs from `backend-api`, so Docker, Prisma, migrations, API tests, and deployment can evolve separately.
- Multiple developers can own separate repos without stepping on app build files or backend runtime files.
- Shared contracts should later become a versioned package, not a hidden folder dependency.

## Mobile Commands

```bash
cd mobile-app
npm run start
npx expo config --type public
```

## Backend Commands

```bash
cd backend-api
npm run build
npm test
docker compose up -d db redis api
```

For Prisma work:

```bash
npm run prisma:generate
docker compose exec api npm run prisma:migrate
```

## Rule For Future Work

- Mobile UI, Expo config, mobile assets, and mobile dependencies go under `mobile-app`.
- Backend API, Prisma, Docker runtime, auth, jobs, and backend dependencies go under `backend-api`.
- The parent folder is only for planning docs while the repos are being prepared.
