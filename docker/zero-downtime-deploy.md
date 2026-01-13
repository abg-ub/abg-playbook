# 🚀 Zero‑Downtime Deployment (Docker + Traefik, No Registry)

This repository documents how to deploy and update a **Next.js frontend** using **Docker Compose + Traefik** with **near zero‑downtime**, **without Docker Swarm** and **without pushing images to a registry**.

The guide is written so **any developer** can follow it step by step.

---

## 📦 Tech Stack

* Next.js
* Docker
* Docker Compose
* Traefik (reverse proxy)
* Single VPS
* No Docker Swarm
* No Docker Registry (no push / pull)

---

## 🧠 Core Idea (Simple Explanation)

Instead of stopping the app and starting it again, we:

1. Run **two containers** of the same app
2. Start the **new version** while the **old version is still running**
3. Let **Traefik load‑balance traffic** between them
4. Stop the old container **only after** the new one is healthy

Result: **near zero‑downtime deployments**.

---

## 📁 Project Structure

```
.
├── Dockerfile
├── docker-compose.yml
├── .env
├── app/
│   └── api/
│       └── health/
│           └── route.ts
└── README.md
```

---

## ❤️ Health Check (Required)

Traefik only routes traffic to **healthy containers**.

### Next.js health endpoint

```ts
// app/api/health/route.ts
export async function GET() {
  return Response.json({ status: "ok" })
}
```

Accessible at:

```
/api/health
```

---

## 🐳 Dockerfile

```dockerfile
# syntax=docker.io/docker/dockerfile:1

FROM node:20-alpine AS base

# Install dependencies only when needed
FROM base AS deps
# Check https://github.com/nodejs/docker-node/tree/b4117f9333da4138b03a546ec926ef50a31506c3#nodealpine to understand why libc6-compat might be needed.
RUN apk add --no-cache libc6-compat
WORKDIR /app

# Install dependencies based on the preferred package manager
COPY package.json yarn.lock* package-lock.json* pnpm-lock.yaml* .npmrc* ./
RUN \
  if [ -f yarn.lock ]; then yarn --frozen-lockfile; \
  elif [ -f package-lock.json ]; then npm ci --legacy-peer-deps; \
  elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm i --frozen-lockfile; \
  else echo "Lockfile not found." && exit 1; \
  fi



# Rebuild the source code only when needed
FROM base AS builder
WORKDIR /app
COPY --from=deps /app/node_modules ./node_modules
COPY . .

# Next.js collects completely anonymous telemetry data about general usage.
# Learn more here: https://nextjs.org/telemetry
# Uncomment the following line in case you want to disable telemetry during the build.
# ENV NEXT_TELEMETRY_DISABLED=1

RUN \
  if [ -f yarn.lock ]; then yarn run build; \
  elif [ -f package-lock.json ]; then npm run build; \
  elif [ -f pnpm-lock.yaml ]; then corepack enable pnpm && pnpm run build; \
  else echo "Lockfile not found." && exit 1; \
  fi

# Production image, copy all the files and run next
FROM base AS runner
WORKDIR /app

ENV NODE_ENV=production
# Uncomment the following line in case you want to disable telemetry during runtime.
# ENV NEXT_TELEMETRY_DISABLED=1

RUN addgroup --system --gid 1001 nodejs
RUN adduser --system --uid 1001 nextjs

COPY --from=builder /app/public ./public

# Automatically leverage output traces to reduce image size
# https://nextjs.org/docs/advanced-features/output-file-tracing
COPY --from=builder --chown=nextjs:nodejs /app/.next/standalone ./
COPY --from=builder --chown=nextjs:nodejs /app/.next/static ./.next/static

USER nextjs

EXPOSE 3000

ENV PORT=3000

# server.js is created by next build from the standalone output
# https://nextjs.org/docs/pages/api-reference/config/next-config-js/output
ENV HOSTNAME="0.0.0.0"
CMD ["node", "server.js"]
```

This setup supports **graceful shutdown** when containers are stopped.

---

## 🧩 Docker Compose Configuration

> ⚠️ Important rules:
>
> * **DO NOT** use `container_name`
> * **DO NOT** expose ports with `ports:`
> * Traefik handles all routing

```yaml
---
services:
  frontend:
    build:
      context: .
      dockerfile: Dockerfile

    restart: always
    stop_grace_period: 30s

    env_file:
      - .env

    healthcheck:
      test:
        [
          "CMD",
          "node",
          "-e",
          "require('http').get('http://localhost:3000/api/health', res => process.exit(res.statusCode === 200 ? 0 : 1)).on('error', () => process.exit(1))"
        ]
      interval: 15s
      timeout: 5s
      retries: 3
      start_period: 30s

    labels:
      - traefik.enable=true
      - traefik.docker.network=traefik-net

      - traefik.http.routers.your-custom-name.rule=Host(`your-hostname`)
      - traefik.http.routers.your-custom-name.entrypoints=websecure
      - traefik.http.routers.your-custom-name.tls=true
      - traefik.http.routers.your-custom-name.tls.certresolver=letsencrypt
      - traefik.http.services.your-custom-name.loadbalancer.server.port={app-internal-port}

    networks:
      - traefik-net

networks:
  traefik-net:
    external: true
```

---

## ▶️ First Deployment

```bash
docker compose up -d
```

This runs a single container:

```
frontend_1
```

Your application is now live.

---

## 🔁 Zero‑Downtime Deployment (Update Flow)

### 1️⃣ Pull latest code

```bash
git pull
```

---

### 2️⃣ Start second container (new version)

```bash
docker compose up -d --build --scale frontend=2
```

What happens internally:

* Old container keeps serving users
* New container is built and started
* Health check passes
* Traefik routes traffic to both containers cause of loadbalancer

At this point:

```
frontend_1 → old version
frontend_2 → new version
```

---

### 3️⃣ Stop old container safely

```bash
docker compose up -d --scale frontend=1
```

* Traefik stops sending traffic to the old container
* Existing requests finish
* Old container shuts down gracefully

Users experience **no downtime**.

---

## 🔄 Rollback (If Something Breaks)

```bash
git checkout <previous-commit>
docker compose up -d --build --scale frontend=2
docker compose up -d --scale frontend=1
```

---

## 📈 Scaling Reference

| Command              | Description              |
| -------------------- | ------------------------ |
| `--scale frontend=1` | Normal operation         |
| `--scale frontend=2` | Zero‑downtime deployment |
| `--scale frontend=3` | High traffic             |
| `--scale frontend=0` | Stop service             |

---

## 🚫 Common Mistakes (Avoid These)

* ❌ Using `container_name`
* ❌ Running only one container during deploy
* ❌ Using `docker compose down`
* ❌ Exposing ports with `ports:`
* ❌ No health check endpoint

---

## ⚠️ Limitations

* No CI/CD
* No instant rollback
* Single VPS only
* Builds consume VPS CPU/RAM

This setup is **ideal for single‑server production environments**.

---

## 🧠 Summary

* Two containers run in parallel
* Traefik load‑balances traffic
* New version starts before old stops
* Near zero‑downtime without Swarm or registry

---

## 👨‍💻 Maintainer

* ABG
