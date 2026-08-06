# Hermes Agent Deployment for Dokploy

This folder contains the Docker Compose configuration and environment templates required to deploy **Hermes Agent** (by Nous Research) on [Dokploy](https://dokploy.com).

---

## 📁 Files Included

- [`docker-compose.yml`](file:///Users/soumajit/Developer/github-profile-soumajitgh/deploy/hermes/docker-compose.yml): Docker Compose configuration with environment-driven port variables.
- [`.env.example`](file:///Users/soumajit/Developer/github-profile-soumajitgh/deploy/hermes/.env.example): Environment variable template for custom service ports, API keys, and bot tokens.

---

## 🚀 Deployment Instructions in Dokploy

### 1. Create a Compose Application
1. Log in to your **Dokploy** Dashboard.
2. Navigate to your Project and click **Create Service** → Select **Compose**.
3. Set the application name to `hermes-agent`.

### 2. Configure Source & Compose Path
- **If using Git repository**:
  - Select your Git provider and repository.
  - Set the Compose path to: `deploy/hermes/docker-compose.yml`.
- **If deploying directly via raw YAML**:
  - Paste the contents of [`docker-compose.yml`](file:///Users/soumajit/Developer/github-profile-soumajitgh/deploy/hermes/docker-compose.yml) into the Dokploy Compose Editor.

### 3. Set Environment Variables
In the **Environment** tab of your Compose application in Dokploy, copy key-value pairs from [`.env.example`](file:///Users/soumajit/Developer/github-profile-soumajitgh/deploy/hermes/.env.example) and set your custom ports and credentials:

```env
HERMES_PORT=8642
DASHBOARD_PORT=9119

OPENAI_API_KEY=sk-...
ANTHROPIC_API_KEY=sk-ant-...
TELEGRAM_BOT_TOKEN=...

HERMES_DASHBOARD_BASIC_AUTH_USERNAME=admin
HERMES_DASHBOARD_BASIC_AUTH_PASSWORD=a-strong-password
HERMES_DASHBOARD_BASIC_AUTH_SECRET=$(openssl rand -hex 32)
```

The dashboard binds `0.0.0.0` and Hermes fail-closes on any non-loopback
bind: it won't start without `HERMES_DASHBOARD_BASIC_AUTH_*` (or an OIDC
provider — see [docs](https://hermes-agent.nousresearch.com/docs/user-guide/features/web-dashboard))
set. Generate `HERMES_DASHBOARD_BASIC_AUTH_SECRET` with `openssl rand -hex 32`.

### 4. Setup Domains & Reverse Proxy Routing (Dokploy Domain Tab)
In Dokploy's **Domains** tab for this deployment:
- **Dashboard UI**: Add a domain pointing to service `dashboard` on container port `${DASHBOARD_PORT}` (default `9119`).
- **API Gateway**: Add a domain pointing to service `hermes` on container port `${HERMES_PORT}` (default `8642`).

Dokploy will handle SSL termination and proxy requests directly to the respective services.

### 5. Persistent Storage
The deployment defines a volume `hermes_data` mounted at `/opt/data` to ensure all agent memories, skills, and settings persist across restarts.
