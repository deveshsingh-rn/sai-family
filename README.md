# Sai Family Project Split

This folder now contains two independent app folders that can be pushed to two different Git repositories.

## Repositories

- `mobile-app/` is the Expo React Native mobile app.
- `backend-api/` is the Node.js Express backend with Docker, Prisma, Postgres/PostGIS, and Redis.

Each folder has its own `package.json`, `package-lock.json`, `.gitignore`, and README.

## Mobile

```bash
cd mobile-app
npm run start
```

## Backend

```bash
cd backend-api
npm run build
npm test
docker compose up -d db redis api
```

Planning docs remain in `docs/` for now.
