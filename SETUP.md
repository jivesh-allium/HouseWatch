# HouseWatch Setup Guide

Quick guide to run HouseWatch with different ClickHouse instances.

## Prerequisites

- Docker and Docker Compose installed
- Access to a ClickHouse instance (ClickHouse Cloud or Altinity)

## Setup

### 1. Build the Frontend (One-time setup)

```bash
cd frontend
pnpm install
pnpm run build
cd ..
```

### 2. Configure Your Environment

Create `.env.environment-a`:

```bash
CLICKHOUSE_HOST=your-instance.clickhouse.cloud:9440
CLICKHOUSE_USER=default
CLICKHOUSE_PASSWORD=your_password_here
CLICKHOUSE_SECURE=True

# Optional: Enable AI features
OPENAI_API_KEY=sk-...
OPENAI_MODEL=gpt-5-nano
```

If you have multiple environments, you can set up a `.env` for each eg: `.env.environment-b`:

```bash
CLICKHOUSE_HOST=your-instance.altinity.cloud:9440
CLICKHOUSE_USER=admin
CLICKHOUSE_PASSWORD=your_password_here
CLICKHOUSE_SECURE=True

# Optional: Enable AI features
OPENAI_API_KEY=sk-...
OPENAI_MODEL=gpt-5-nano
```

### 3. Run HouseWatch

**Using ClickHouse Cloud:**

```bash
SITE_ADDRESS=":8001" docker compose --env-file .env.environment-a -f docker-compose.yml up -d
```

### 4. Access HouseWatch

Open your browser to: **<http://localhost:8001>**

## Switching Between Profiles

Simply stop the current instance and start with a different env file:

```bash
# Stop current
docker compose -f docker-compose.yml down

# Start with different profile
SITE_ADDRESS=":8001" docker compose --env-file .env.environment-b -f docker-compose.yml up -d
```

## Configuration Reference

### Required Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `CLICKHOUSE_HOST` | ClickHouse host with port | `instance.cloud:9440` |
| `CLICKHOUSE_USER` | Username | `admin` or `default` |
| `CLICKHOUSE_PASSWORD` | Password | `your_password` |
| `CLICKHOUSE_SECURE` | Use secure connection | `True` |

### Optional Variables

| Variable | Description | Default |
|----------|-------------|---------|
| `CLICKHOUSE_DATABASE` | Default database | `default` |
| `CLICKHOUSE_CLUSTER` | Cluster name | `mycluster` |
| `CLICKHOUSE_VERIFY` | Verify SSL certificates | `True` |
| `CLICKHOUSE_CA` | Path to CA certificate | - |
| `OPENAI_API_KEY` | OpenAI API key for AI features | - |
| `OPENAI_MODEL` | OpenAI model to use | `gpt-3.5-turbo` |

## Troubleshooting

**Issue: Authentication failed**
- Verify your credentials in the .env file
- Check if you have system environment variables conflicting (especially `CLICKHOUSE_PASSWORD`)

**Issue: 502 Bad Gateway**
- Wait a few seconds for all services to start
- Check logs: `docker compose logs -f`

**Issue: Frontend not loading**
- Ensure frontend was built: `cd frontend && pnpm run build`
- Rebuild web container: `docker compose up -d --build web`

**Issue: Can't connect to ClickHouse**
- Verify the host includes the port (usually `:9440` for secure connections)
- Check `CLICKHOUSE_SECURE=True` is set
- Verify your network can reach the ClickHouse instance
