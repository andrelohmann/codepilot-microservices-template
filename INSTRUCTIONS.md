# INSTRUCTIONS.md — Template Instructions for Multi-Container Microservices Scaffold

> Note: This file is meant for an LLM / Agent (e.g. in VS Code) to fetch and follow.  
> The scaffold prompt will refer to this file by URL. The Agent should download, parse, and then generate the described file structure in a new, empty workspace.

---

## ⚠️ CRITICAL: Do NOT Clone the Repository

**IMPORTANT:** You must **NOT** clone the template repository using `git clone` or any git commands.

Instead:
- ✅ **Fetch individual files** from the repository using HTTP/HTTPS requests
- ✅ **Copy file contents directly** to the new workspace
- ✅ **Create files programmatically** with the fetched content
- ❌ **DO NOT use git clone** — this would copy the entire git history
- ❌ **DO NOT copy the .git/ directory** — the new workspace needs its own git repository

**Why?** The new workspace should be a clean copy of the template structure without the template's git history. Cloning would bring unwanted git metadata and history.

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
├── templates/          ← Template files (not copied to new workspace)
│   ├── .github/        ← Copy contents to root .github/ in new workspace
│   └── .vscode/        ← Copy contents to root .vscode/ in new workspace
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

**CRITICAL:** The agent must copy files **EXACTLY AS-IS** from the template repository. Do NOT modify, rename, or edit any file contents.

### 🚨 File Copying Rules:

1. **Exact Copy Only**: Copy file contents byte-for-byte without any modifications
2. **Preserve File Names**: Use the exact same filename from the source
3. **Preserve File Structure**: Maintain the exact directory structure
4. **No Content Editing**: Do NOT change file contents, even if they seem incorrect or incomplete
5. **No Interpretation**: Do NOT interpret placeholders, comments, or instructions within files
6. **No Summarization**: Do NOT summarize or abbreviate file contents
7. **Binary Files**: Copy binary files (images, etc.) exactly as-is

### File Fetching Method:

Use GitHub raw file URLs to fetch exact file contents:
```
https://raw.githubusercontent.com/andrelohmann/copilot-microservices-template/main/{filepath}
```

For directory listings, use GitHub API:
```
https://api.github.com/repos/andrelohmann/copilot-microservices-template/contents/{dirpath}
```

The agent should copy **ALL** files from the template repository to the new workspace, with the following exceptions:

### ✅ Files to Copy (Exact Copy Required):

1. **Root level files:**
   - `.gitignore` → copy to `.gitignore`
   - `LICENSE` → copy to `LICENSE`
   - `docker-compose.yml` → copy to `docker-compose.yml`
   - `docker-compose.development.yml` → copy to `docker-compose.development.yml`

2. **`.devcontainer/` directory:**
   - Fetch all files from `https://api.github.com/repos/andrelohmann/copilot-microservices-template/contents/.devcontainer`
   - Copy each file to `.devcontainer/{filename}` with exact content
   - Include `.gitkeep`

3. **`.github/` directory** (source: `templates/.github/`):
   - Fetch from `templates/.github/` in repository
   - Copy to `.github/` in new workspace (root level)
   - Preserve all subdirectories and files:
     - `templates/.github/copilot-instructions.md` → `.github/copilot-instructions.md`
     - All files in `templates/.github/instructions/` → `.github/instructions/{filename}`
     - All files in `templates/.github/prompts/` → `.github/prompts/{filename}`
     - All files in `templates/.github/chatmodes/` → `.github/chatmodes/{filename}`

4. **`.vscode/` directory** (source: `templates/.vscode/`):
   - Fetch from `templates/.vscode/` in repository
   - Copy to `.vscode/` in new workspace (root level)
   - Files:
     - `templates/.vscode/extensions.json` → `.vscode/extensions.json`
     - `templates/.vscode/settings.json` → `.vscode/settings.json`

5. **`config/` directory:**
   - `config/.env.example` → copy to `config/.env.example`

6. **`services/` directory:**
   - `services/.gitkeep` → copy to `services/.gitkeep`

7. **`backing-services/` directory:**
   - `backing-services/.gitkeep` → copy to `backing-services/.gitkeep`

8. **`development-services/` directory:**
   - Recursively copy all subdirectories and files
   - Example: `development-services/openai-api/entrypoint.sh` → `development-services/openai-api/entrypoint.sh`
   - Include `development-services/.gitkeep`
   - Preserve file permissions (especially for shell scripts)

9. **`tmp/` directory:**
   - `tmp/.gitkeep` → copy to `tmp/.gitkeep`
   - Do NOT create subdirectories (they will be created automatically by Docker volumes at runtime)

### 🔍 File Copy Verification:

After copying each file, verify:
1. **File exists** in the new workspace at the correct path
2. **File size matches** the source file exactly
3. **File content is identical** - no modifications, no truncation
4. **File name is exact** - same case, same extension
5. **Directory structure preserved** - all subdirectories created

### ❌ Files NOT to Copy:

1. **`README.md`** — Do not copy if already exists in target workspace
2. **`INSTRUCTIONS.md`** — Never copy this file to the target workspace
3. **`.git/` directory** — Never copy version control data
4. **`tmp/` subdirectories** — Only copy `tmp/.gitkeep`, NOT any subdirectories like `.redis/`, `.ollama/` (created at runtime by Docker)
5. **`templates/` folder itself** — Do NOT copy the templates folder; instead, copy its contents (`.github/`, `.vscode/`) to the root of the new workspace

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
   - Copy `templates/.github/` to `.github/` in new workspace (all subdirectories and files)
   - Copy `templates/.vscode/` to `.vscode/` in new workspace (all files)
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
3. **DO NOT CLONE** the template repository — fetch files individually via HTTP/HTTPS
4. **Create the complete directory structure** as specified above
5. **Copy all template files** from the source repository (https://github.com/andrelohmann/copilot-microservices-template) by fetching individual files, respecting the exceptions:
   - Copy contents of `templates/.github/` to `.github/` in new workspace
   - Copy contents of `templates/.vscode/` to `.vscode/` in new workspace
   - Do NOT copy the `templates/` folder itself
   - Copy everything else EXCEPT `README.md` (if exists), `INSTRUCTIONS.md`, `.git/`, `templates/` folder, and `tmp/` data
6. **Preserve placeholders** — do not resolve variables like `${serviceName}`
7. **Handle conflicts gracefully** — never overwrite `README.md` if it exists
8. **Copy `.gitkeep` placeholder files** as found in the template (do NOT create `tmp/` subdirectories)
9. **Understand the marker system** in docker-compose files for future service additions
10. **Output a complete summary** of all created files and directories at the end

### File Fetching Methods:
Use one of these approaches to fetch files without cloning:
- GitHub API: `https://api.github.com/repos/andrelohmann/copilot-microservices-template/contents/{path}`
- Raw file URL: `https://raw.githubusercontent.com/andrelohmann/copilot-microservices-template/main/{path}`
- Direct HTTP requests to fetch individual file contents

### Expected Result:
After running the scaffold prompt, the new workspace should be a **near 100% copy** of the template repository, with all files, directory structure, configuration, and placeholders in place — ready for developers to start adding their own microservices.
