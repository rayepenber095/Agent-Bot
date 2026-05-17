# Agent-Bot (VulnLab Pro) — Kali Linux Setup Guide

This repository is a pnpm monorepo with:
- **Backend API**: `artifacts/api-server` (Express + PostgreSQL)
- **Frontend Web App**: `artifacts/vulnlab-pro` (React + Vite)

## 1) Install required software on Kali Linux

```bash
sudo apt update
sudo apt install -y git curl build-essential python3 make g++ postgresql postgresql-contrib
```

Install **Node.js 24** (recommended by this project) and pnpm:

```bash
curl -fsSL https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.3/install.sh | bash
export NVM_DIR="$HOME/.nvm"
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
nvm install 24
nvm use 24
corepack enable
corepack prepare pnpm@9.15.9 --activate
```

Verify:

```bash
node -v
pnpm -v
psql --version
```

## 2) Clone and install dependencies

```bash
git clone https://github.com/rayepenber095/Agent-Bot.git
cd Agent-Bot
pnpm install --no-frozen-lockfile
```

## 3) Prepare PostgreSQL

Start PostgreSQL:

```bash
sudo systemctl enable --now postgresql
```

Create a database and user:

```bash
sudo -u postgres psql
```

In `psql`:

```sql
CREATE USER vulnlab WITH PASSWORD 'vulnlab123';
CREATE DATABASE vulnlab_pro OWNER vulnlab;
\q
```

Set database URL in your shell:

```bash
export DATABASE_URL='postgresql://vulnlab:vulnlab123@localhost:5432/vulnlab_pro'
```

> Important: this repo does **not** include complete SQL migrations/seed files for all required tables/data, so you must load the project’s schema/seed dataset into this database before the app will work fully.

## 4) Run backend API (Terminal 1)

From repo root:

```bash
cd /path/to/Agent-Bot
export PORT=8080
export BASE_PATH=/api
export JWT_SECRET='change-this-to-a-strong-secret'
export DATABASE_URL='postgresql://vulnlab:vulnlab123@localhost:5432/vulnlab_pro'
pnpm --filter @workspace/api-server run dev
```

API health check:

```bash
curl http://localhost:8080/api/healthz
```

## 5) Run frontend web app (Terminal 2)

From repo root:

```bash
cd /path/to/Agent-Bot
export PORT=24452
export BASE_PATH=/
pnpm --filter @workspace/vulnlab-pro run dev
```

Open in browser:
- `http://localhost:24452`

## 6) Optional: run mockup sandbox

```bash
cd /path/to/Agent-Bot
export PORT=8081
export BASE_PATH=/__mockup
pnpm --filter @workspace/mockup-sandbox run dev
```

Open:
- `http://localhost:8081/__mockup`

## 7) Useful workspace commands

```bash
cd /path/to/Agent-Bot
pnpm run typecheck
pnpm run build
```

If typecheck/build fails, some existing issues may already be present in the current branch.
