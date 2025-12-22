# Agent Analytics Platform

> Open-source analytics and monitoring platform for AI agents

## ⚡ Overview

Agent Analytics is a monitoring platform for AI agents with two deployment modes:

### 🎯 In-Memory Mode (Development)
- No external services required
- Data persists only during runtime
- Perfect for development and testing
- Fastest setup

### 🚀 Persistent Mode (Production)
- Requires Elasticsearch + Jaeger instances
- Data persists across restarts
- Configure via proxy-config.yaml
- Production-ready

---

## 🚀 Quick Start

### Prerequisites

- **Python 3.10+**
- **Node.js** >= 20.0.0
- **npm** >= 9.0.0
- [**uv**](https://astral.sh/uv) (Python package manager)
- **Git** (for downloading dependencies)

**For Persistent Mode Only:**
- **Elasticsearch** instance (accessible)
- **Jaeger** instance (accessible)

---

### Setup Steps

#### 1. Clone the Repository
```bash
git clone https://github.com/AgentToolkit/agent-analytics.git
cd agent-analytics/backend
```

#### 2. Configure Environment
```bash
# Copy environment template
cp .env.example .env

# Edit .env to configure settings (optional for in-memory mode)
```

For in-memory mode, default .env works as-is.
For persistent mode, update Elasticsearch/Jaeger URLs in .env.

#### 3. Clean Any Existing Installation (Optional)
```bash
npm run clean
```

This removes existing virtual environments and build artifacts.

#### 4. Install Dependencies
```bash
# Basic setup (recommended)
npm run setup

# Or for development tools (linters, formatters, extra dev dependencies)
npm run setup:dev
```

This installs:
- Python environment (via uv)
- `agent-analytics-common` from GitHub (public repo, no authentication required)
- All Python dependencies
- Node.js dependencies
- Frontend dependencies

#### 5. Start the Application

**In-Memory Mode (Default):**
```bash
npm run dev:memory
```

**Persistent Mode (with Elasticsearch):**
```bash
# First, ensure Elasticsearch and Jaeger are running
# Then configure proxy-config.yaml with your instance URLs

npm run dev
```

Wait for: `Application startup complete`

#### 6. Access the Application

Open http://localhost:8765

- **Mock login page**: Enter any name to proceed
- **Upload test files**: From `tests/runtime/integration_tests/`
- **API Docs**: http://localhost:8765/docs

**During development, also available:**
- **Frontend Dev Server**: http://localhost:3000 (only with `npm run dev:full`)

---

## 🗄️ Persistent Storage Configuration

To enable persistent storage, you need running instances of:
- **Elasticsearch** (for trace data storage)
- **Jaeger** (for distributed tracing)

### Configuration Steps

1. **Update proxy-config.yaml:**
```yaml
tenants:
  10000000-0000-0000-0000-000000000000:
    store_type: elasticsearch
    hostname: "http://your-elasticsearch:9200"
    username: "elastic"
    password: "changeme"
    index_prefix: "10000000-0000-0000-0000-000000000000"
```

2. **Update .env:**
```bash
ES_HOST=http://your-elasticsearch:9200
ES_USERNAME=elastic
ES_PASSWORD=changeme
JAEGER_URL=http://your-jaeger:16686
JAEGER_COLLECT_URL=http://your-jaeger:4318/v1/traces
```

3. **Set the config file in .env:**
```bash
TENANT_CONFIG_FILE=./proxy-config.yaml
```

4. **Start the application:**
```bash
npm run dev
```

---

## 📋 Available Commands

### Development
```bash
# In-memory mode (no external services)
npm run dev:memory

# Persistent mode (requires Elasticsearch/Jaeger)
npm run dev

# Full stack with both dev servers
npm run dev:full

# Quick start (no rebuild)
npm run dev:quick

# Backend only
npm run dev:backend

# Frontend only
npm run dev:frontend
```

### Building
```bash
npm run build            # Build both frontend and backend
npm run build:frontend   # Build React app for production
npm run build:backend    # Build Python package
```

### Testing
```bash
npm run test             # Run all tests
npm run test:frontend    # Frontend tests only
npm run test:platform    # Python platform tests
npm run test:e2e         # End-to-end Cypress tests
```

### Code Quality
```bash
npm run lint             # Lint all code
npm run lint:fix         # Fix linting issues automatically
npm run format           # Format all code
```

### Maintenance
```bash
npm run clean            # Remove all generated files and dependencies
```

---

## 🐛 Debugging

### VSCode Users

1. Install recommended VSCode extensions (you'll be prompted)
2. Set breakpoints in your code
3. Press `F5` or use the Debug panel

**Available debug configurations:**
- **🔧 Debug Python Server**: Debug backend in memory mode
- **⚛️ React Client**: Debug frontend in Chrome
- **🚀 Full Stack**: Debug both frontend and backend
- **🧪 Tests**: Debug test files

---

## 🗃️ Project Structure

```
backend/
├── src/
│   └── agent_analytics/
│       ├── client/          # React frontend
│       ├── server/          # FastAPI backend (main.py)
│       ├── runtime/         # Core platform logic
│       ├── core/            # Common classes and interfaces
│       ├── extensions/      # Analytics extensions
│       └── sdk/             # SDK resources
├── pyproject.toml          # Python dependencies
├── package.json            # npm scripts
├── proxy-config.yaml       # Persistent storage config
└── proxy-config-memory.yaml # In-memory config
```

---

## 🔧 Troubleshooting

### Common Issues

**Static Files Error (`Directory 'src/client/build/static' does not exist`):**
```bash
# Build frontend first
npm run build:frontend

# Or use dev command that auto-builds
npm run dev:memory
```

**Backend Can't Connect to Elasticsearch/Jaeger (Persistent Mode):**
- Verify instances are running and accessible
- Check URLs in `.env` and `proxy-config.yaml`
- Test connectivity: `curl http://your-elasticsearch:9200/_cluster/health`

**Installation Issues:**
```bash
# Clean everything and retry
npm run clean
npm run setup
```

**UV Sync Issues:**
```bash
# Clear UV cache and retry
rm -rf .venv
uv cache clean
npm run setup
```

**Port 8765 Already in Use:**
```bash
# Change port in .env
PORT=8766 npm run dev:memory
```

**Dependencies Download Fails:**
- Ensure Git is installed: `git --version`
- Check internet connection
- Dependencies come from public GitHub repository (no authentication required)

**Windows npm Script Errors:**
```bash
# Configure npm to use bash (if using Git Bash)
npm config set script-shell "C:\\Program Files\\Git\\bin\\bash.exe"
```

**PROJECT_ROOT Issues:**
- The app auto-detects the project root based on the main.py file location
- For local development: don't set PROJECT_ROOT (let it auto-detect)
- For custom paths: set PROJECT_ROOT to absolute path in .env

---

## 🤝 Contributing

1. Fork the repository
2. Create a feature branch: `git checkout -b feature/amazing-feature`
3. Make your changes and ensure tests pass: `npm run test`
4. Run code quality checks: `npm run lint && npm run format`
5. Submit a pull request

---

## 📄 License

This project is licensed under the Apache License 2.0 - see the [LICENSE](LICENSE) file for details.
