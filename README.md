# 🧩 Local n8n Automation Environment

This repository provides a containerized local setup for n8n, powered by Docker and PostgreSQL as the datastore. It also includes a Playwright test runner and MCP server for workflow validation and automated testing.

## 🏗️ Project Structure

```
.
├── docker-compose.yml          # Main Docker Compose configuration
├── Dockerfile.n8n              # Custom n8n image with Docker CLI and Git
├── .env                        # Environment variables (already exists)
├── exported_workflows/
│   └── workflows.json          # Single file containing all exported workflows
├── runner/
│   └── Dockerfile              # Playwright MCP server container
├── artifacts/                  # Test outputs (empty by default)
├── *.png                       # Various screenshot files
└── README.md
```

## 📂 Folder Overview

### `exported_workflows/`
Contains `workflows.json` - a single file with all exported n8n workflows in JSON array format.

> ⚠️ **Note**: Credentials are not included for security reasons — you'll need to reconfigure them manually within n8n.

### `runner/`
Contains a Dockerfile for the Playwright MCP (Model Context Protocol) server. This provides browser automation capabilities for AI agents to perform web testing and automation tasks.

### `artifacts/`
Stores output from test runs — including reports, logs, screenshots, or any other generated artifacts. Currently empty but will be populated during test execution.

## 🚀 Getting Started

### 1. Prerequisites

- Docker
- Docker Compose
- (Optional) Make

### 2. Environment Variables

Create a `.env` file in the project root with the following variables:

```env
# Postgres Database
POSTGRES_USER=n8n
POSTGRES_PASSWORD=your-secure-password
POSTGRES_DB=n8n

# n8n Configuration
N8N_ENCRYPTION_KEY=your-encryption-key-here
GENERIC_TIMEZONE=Asia/Kathmandu

# Docker Configuration  
DOCKER_GID=144
```

> **Important**: 
> - The `N8N_ENCRYPTION_KEY` is required for credential encryption. Generate one with `openssl rand -hex 32`
> - Replace `your-secure-password` with a strong PostgreSQL password
> - `DOCKER_GID` should match your system's docker group ID (find with `getent group docker`)
> - The `.env` file is excluded from git for security reasons

### 3. Start the Stack

```bash
docker-compose up -d
```

This will start three services:
- **PostgreSQL** - Database backend for n8n
- **n8n** - Main automation platform (from Dockerfile.n8n with Docker CLI and Git)
- **mcp-runner** - Playwright MCP server for browser automation (port 8931)

Once started, access the services:
- 🎯 **n8n UI**: http://localhost:5678
- 🤖 **MCP Server**: http://localhost:8931 (for AI agent browser automation)

### 4. Import Workflows

From the n8n UI:
1. Click the menu → Import from File
2. Select the `exported_workflows/workflows.json` file.
3. This will import the clean workflows.
4. Reconfigure credentials as needed for each imported workflow

## 🧪 Browser Automation (Playwright + MCP)

The `mcp-runner` service provides a Model Context Protocol (MCP) server that enables AI agents to perform browser automation tasks.

### Automatic MCP Server
The MCP server starts automatically when you run `docker-compose up -d`:
- **Service**: `mcp-runner` 
- **Port**: 8931
- **Capabilities**: Full Playwright browser automation via MCP protocol

### Manual Testing (Optional)
If you want to run the MCP server separately for testing:

```bash
# Build the runner image manually
docker build -t mcp-runner ./runner

# Run MCP server standalone
docker run --rm -p 8931:8931 mcp-runner
```

### Usage in Workflows
The MCP server is designed to be used by AI agents in your n8n workflows:
- AI agents can connect to `http://mcp-runner:8931/mcp`
- Supports full browser automation: navigation, clicking, form filling, screenshot capture
- Artifacts are shared with n8n via mounted `/srv/n8n/artifacts` volume

## 🚀 Available Workflows

### 1. **Multi-Agent Test Automation**
- AI-powered website testing with Playwright MCP integration
- Screenshot capture and test report generation  
- Webhook triggers for external test execution
- Email notifications with test results

### 2. **Playwright Test Execution**
- GitHub repository cloning and test execution
- Dynamic test configuration via webhooks
- Comprehensive test reporting with artifacts
- Jenkins integration for CI/CD pipelines

## 🧰 Maintenance & Tips

### To stop all containers:
```bash
docker-compose down
```

### To rebuild services after changes:
```bash
# Rebuild n8n service
docker-compose build n8n

# Rebuild MCP runner service  
docker-compose build mcp-runner

# Rebuild all services
docker-compose build
```

### To reset all data (⚠️ deletes database volumes):
```bash
docker-compose down -v
```

### View service logs:
```bash
# View all logs
docker-compose logs -f

# View specific service logs
docker-compose logs -f n8n
docker-compose logs -f mcp-runner
docker-compose logs -f postgres
```

## 📜 Notes

- **Security**: Credentials and environment variables are intentionally excluded from version control
- **Git Ignored Files**: The following are excluded via `.gitignore`:
  - `.env` files (contains sensitive credentials)
  - `artifacts/` directory (test outputs and screenshots)
  - Docker volumes and temporary files
  - IDE configuration files
- **Workflows**: All workflows are stored in a single `workflows.json` file for easy backup/restore
- **Artifacts**: Test results and screenshots are stored in `/srv/n8n/artifacts` (shared between n8n and MCP server)
- **MCP Integration**: The Playwright MCP server enables AI agents to perform real browser automation
- **Multi-Environment**: Supports different environments via webhook parameters (qa, staging, production)

## 🧑‍💻 Author Notes

This setup is ideal for:
- Local development and testing of n8n workflows
- Automated validation via Playwright
- CI/CD integrations where artifacts and workflows need to be versioned separately

---

**License**: MIT  
**Maintained by**: Ujjwal Khanal 