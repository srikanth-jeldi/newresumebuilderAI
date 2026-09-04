# Resumaire n8n — Docker Self-Hosted Setup

This stack runs n8n locally with PostgreSQL persistence. It is intended for development and automation setup. Production exposure requires HTTPS/reverse proxy hardening.

## Prerequisites
- Docker Desktop (Windows/macOS) or Docker Engine + Compose plugin (Linux)
- Git
- Port 5678 available

## 1. Clone and enter the project
```bash
git clone https://github.com/srikanth-jeldi/newresumebuilderAI.git
cd newresumebuilderAI
git checkout setup/architect-agent
cd automation/n8n/self-hosted
```

## 2. Create local environment file
Windows PowerShell:
```powershell
Copy-Item .env.example .env
```

Linux/macOS:
```bash
cp .env.example .env
```

Edit `.env` and replace the database password and encryption key.

Generate a strong encryption key.

PowerShell example:
```powershell
$bytes = New-Object byte[] 48
[Security.Cryptography.RandomNumberGenerator]::Fill($bytes)
[Convert]::ToBase64String($bytes)
```

Linux/macOS example:
```bash
openssl rand -base64 48
```

Keep `N8N_ENCRYPTION_KEY` stable. Do not rotate it casually after credentials have been stored.

## 3. Start n8n
```bash
docker compose up -d
```

Check:
```bash
docker compose ps
```

Logs:
```bash
docker compose logs -f n8n
```

Open locally:
`http://localhost:5678`

On first launch, create the n8n owner account in the browser.

## 4. Verify persistence
Create a temporary workflow, then restart:
```bash
docker compose restart
```

The workflow should remain because both n8n data and PostgreSQL use Docker named volumes.

## 5. Stop/start commands
Stop containers without deleting data:
```bash
docker compose stop
```

Start again:
```bash
docker compose start
```

Stop and remove containers but keep named volumes:
```bash
docker compose down
```

WARNING — this deletes n8n/PostgreSQL persistent volumes:
```bash
docker compose down -v
```
Do not use `-v` unless you intentionally want to destroy local n8n state.

## 6. Update n8n
For local development the example uses the current n8n image tag from `.env`.
Before production, pin a tested n8n version instead of floating `latest`.

Typical update:
```bash
docker compose pull
docker compose up -d
```
Always back up the n8n database and encryption key before a production upgrade.

## 7. Credentials to configure inside n8n
After n8n starts, add credentials through the n8n UI — never put them in Git:

### GitHub
Create/connect a GitHub credential that can access:
`srikanth-jeldi/newresumebuilderAI`

Minimum automation requirements:
- read issues
- create/update issues/comments
- read repository files
- later create branches/PRs only when the workflow explicitly needs them

### OpenAI
Add the OpenAI API credential in n8n Credentials. Do not paste the API key into workflow node JSON or GitHub files.

## 8. First Resumaire automation
The first workflow will process GitHub issue `ORCH-001` (#2):

```text
GitHub Issue Trigger / polling
        ↓
Check repo == newresumebuilderAI
        ↓
Check label status:new
        ↓
Check agent label
        ↓
Load agent SYSTEM.md + WORKFLOW.md + task issue
        ↓
Call AI model
        ↓
Post structured completion/handoff comment
        ↓
Update status label
        ↓
Unlock dependent task
```

Never allow the first workflow to merge to main or deploy production automatically.

## 9. Localhost webhook limitation
A GitHub webhook cannot call `http://localhost:5678` from the public internet.
For the first local test use scheduled GitHub polling, or expose n8n using a controlled HTTPS tunnel.
For production use a real HTTPS domain/reverse proxy and set `N8N_WEBHOOK_URL` accordingly.

## 10. Production minimum hardening
Before using this on a VPS:
- HTTPS only
- reverse proxy (Caddy/Nginx/Traefik)
- firewall; expose only 80/443 publicly
- do not expose PostgreSQL
- strong owner account password
- stable encryption key stored outside Git
- database backups
- n8n version pinning
- regular updates
- separate production credentials
- webhook signature/secret validation where supported

## Current safe milestone
The goal of this setup is only:
1. n8n runs locally.
2. Data survives restart.
3. GitHub and OpenAI credentials can be added securely.
4. ORCH-001 can be read by an automation.
5. No code merge/deploy occurs automatically yet.
