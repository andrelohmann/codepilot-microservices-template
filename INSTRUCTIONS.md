# INSTRUCTIONS.md — Template Instructions for Multi-Container Microservices Scaffold

> Note: This file is meant for an LLM / Agent (e.g. in VS Code) to fetch and follow.  
> The scaffold prompt will refer to this file by URL. The Agent should download, parse, and then generate the described file structure in a new, empty workspace.

---

## 📁 Desired Directory Structure & Core Components

The newly scaffolded workspace should include the following structure:

```
/ (workspace root)
│  docker-compose.yml
│  docker-compose.development.yml
│  .gitignore
│  LICENSE
│  README.md          ← Minimal README if one does not already exist
│
├── .devcontainer/
│   └── .gitkeep
│
├── .github/
│   ├── copilot-instructions.md
│   ├── instructions/
│   │   └── (additional instruction files)
│   ├── prompts/
│   │   └── *.prompt.md
│   └── chatmodes/
│       └── *.chatmode.md
│
├── .vscode/
│   ├── extensions.json
│   └── settings.json
│
├── config/
│   └── .env.example    ← Environment configuration template
│
├── services/
│   └── .gitkeep        ← Placeholder (services will be added by developers)
│
├── backing-services/
│   └── .gitkeep        ← Placeholder (backing services like databases, caches)
│
├── development-services/
│   └── .gitkeep        ← Placeholder (dev-only services like Ollama, mocks)
│
└── tmp/                ← Runtime data directory (gitignored, created at runtime)
    └── .gitkeep
```

---

## 🔧 Docker Compose Marker System

The `docker-compose.yml` and `docker-compose.development.yml` files use a marker system to allow agents to insert new services programmatically:

### Markers in docker-compose.yml and docker-compose.development.yml:

- `##<<BACKING_SERVICES>>` / `##</BACKING_SERVICES>` — Insert backing services (databases, caches, queues) here
- `##<<SERVICES>>` / `##<</SERVICES>>` — Insert application services (APIs, frontends, workers) here
- `##<<DEVELOPMENT_SERVICES>>` / `##</DEVELOPMENT_SERVICES>` — Insert development-only services here (only in docker-compose.development.yml)

**Usage:** When an agent needs to add a new service, it should insert the service definition between the appropriate markers, preserving the marker comments.

**Example:**
```yaml
##<<SERVICES>>
# (The agent will add services here)

my-new-service:
  build: ./services/my-new-service
  ports:
    - "3000:3000"
##<</SERVICES>>
```

---

## 📄 Environment Configuration (.env)

- **Template file:** `config/.env.example` contains all environment variable definitions with example values
- **Active file:** Developers should copy `config/.env.example` to `config/.env` and customize values
- **Security:** The `.env` file is gitignored and contains sensitive data (API keys, passwords, etc.)
- **Reference:** Both compose files reference the env file via `env_file: [./config/.env]`

**Agent Note:** When scaffolding, copy `config/.env.example` from the template. Do NOT create a `config/.env` file automatically.

---

## 📦 Complete File Copying Instructions

The agent should copy **ALL** files from the template repository to the new workspace, with the following exceptions:

### ✅ Files to Copy:

1. **Root level files:**
   - `.gitignore`
   - `LICENSE`
   - `docker-compose.yml`
   - `docker-compose.development.yml`

2. **`.devcontainer/` directory:**
   - All files including `.gitkeep`

3. **`.github/` directory:**
   - `copilot-instructions.md`
   - `instructions/` (all files)
   - `prompts/` (all *.prompt.md files)
   - `chatmodes/` (all *.chatmode.md files)

4. **`.vscode/` directory:**
   - `extensions.json`
   - `settings.json`

5. **`config/` directory:**
   - `.env.example`

6. **`services/` directory:**
   - `.gitkeep` (placeholder only, no example services)

7. **`backing-services/` directory:**
   - `.gitkeep` (placeholder only)

8. **`development-services/` directory:**
   - All subdirectories and files (e.g., `openai-api/entrypoint.sh`)
   - `.gitkeep`

9. **`tmp/` directory:**
   - Copy only the `.gitkeep` file
   - Do NOT create subdirectories (they will be created automatically by Docker volumes at runtime)

### ❌ Files NOT to Copy:

1. **`README.md`** — Do not copy if already exists in target workspace
2. **`INSTRUCTIONS.md`** — Never copy this file to the target workspace
3. **`.git/` directory** — Never copy version control data
4. **`tmp/` subdirectories** — Only copy `tmp/.gitkeep`, NOT any subdirectories like `.redis/`, `.ollama/` (created at runtime by Docker)

---

## 🚫 Special Handling Rules

### README.md:
- If `README.md` exists in target workspace: **DO NOT OVERWRITE**
- If `README.md` does not exist: Create a minimal README explaining the workspace structure

### Placeholders:
- When copying files that include placeholders like `${serviceName}` or `${workspaceFolder}`, **DO NOT RESOLVE** them — leave them intact
- These placeholders will be resolved later by the development environment or by specific service creation commands

### File Conflicts:
- For any file conflict (file already exists), the agent should:
  1. Check if the file is identical (skip if same)
  2. If different, notify the user and ask: overwrite, skip, or show diff
  3. For configuration files like `.gitignore`, consider merging rather than replacing

---

## 🗂 Creation Order & Best Practices

Follow this order for optimal scaffolding:

1. **Create directory structure:**
   - Create all directories first (`.devcontainer/`, `.github/`, `.vscode/`, `config/`, `services/`, `backing-services/`, `development-services/`, `tmp/`)

2. **Copy root-level files:**
   - `.gitignore`, `LICENSE`, `docker-compose.yml`, `docker-compose.development.yml`

3. **Copy configuration directories:**
   - `.github/` (all subdirectories and files)
   - `.vscode/` (all files)
   - `config/.env.example`

4. **Create placeholder directories:**
   - Copy `.gitkeep` files to `services/`, `backing-services/`, `development-services/`, `.devcontainer/`

5. **Copy development services:**
   - Copy complete `development-services/` subdirectories (e.g., `openai-api/`)

6. **Copy tmp directory:**
   - Copy `tmp/.gitkeep` only (runtime subdirectories will be created by Docker volumes)

7. **Final summary:**
   - Output a comprehensive list of all created files and directories

---

## 🧠 Agent Execution Instructions (Natural Language Summary)

When an agent receives a scaffold prompt referencing this INSTRUCTIONS.md, it should:

1. **Fetch and parse** this INSTRUCTIONS.md file from the provided URL
2. **Validate** the current workspace is appropriate for scaffolding (empty or minimal)
3. **Create the complete directory structure** as specified above
4. **Copy all template files** from the source repository (https://github.com/andrelohmann/copilot-microservices-template), respecting the exceptions:
   - Copy everything EXCEPT `README.md` (if exists), `INSTRUCTIONS.md`, `.git/`, and `tmp/` data
5. **Preserve placeholders** — do not resolve variables like `${serviceName}`
6. **Handle conflicts gracefully** — never overwrite `README.md` if it exists
7. **Copy `.gitkeep` placeholder files** as found in the template (do NOT create `tmp/` subdirectories)
8. **Understand the marker system** in docker-compose files for future service additions
9. **Output a complete summary** of all created files and directories at the end

### Expected Result:
After running the scaffold prompt, the new workspace should be a **near 100% copy** of the template repository, with all files, directory structure, configuration, and placeholders in place — ready for developers to start adding their own microservices.
