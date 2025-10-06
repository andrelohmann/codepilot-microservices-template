# Codepilot Microservices Template

This repository serves as a **blueprint** for initializing new workspaces for **microservices applications** using the **VS Code Multiple Containers pattern**.  
It provides the fundamental directory structure, Docker Compose files, and configuration folders that serve as a foundation for Services, Backing-Services, and Development-Services.

This template leverages VS Code's powerful features for multi-container development and AI-assisted coding:
- **[Multiple Containers](https://code.visualstudio.com/remote/advancedcontainers/connect-multiple-containers)** – Connect to multiple Docker containers and switch between microservices seamlessly
- **[Custom Copilot Instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)** – Define workspace-specific AI behavior and conventions
- **[Prompt Files](https://code.visualstudio.com/docs/copilot/customization/prompt-files)** – Create reusable prompts for common development tasks
- **[Custom Chat Modes](https://code.visualstudio.com/docs/copilot/customization/custom-chat-modes)** – Build specialized chat modes for specific workflows

---

## 📂 Directory Structure

### Template Repository Structure

- `.devcontainer/` – VS Code Dev Container configuration  
- `templates/` – Template files that will be copied to new workspaces
  - `.github/` – GitHub-specific files (Copilot instructions, prompts, chatmodes)
    - `copilot-instructions.md` – Main Copilot instructions
    - `chatmodes/` – Custom chat modes
    - `instructions/` – Additional instruction files (e.g., compose-guardrails)
    - `prompts/` – Reusable prompt templates
  - `.vscode/` – VS Code workspace settings (extensions, settings)
- `config/` – Centralized configuration files  
  - `.env.example` – Environment variable template
- `services/` – Placeholder for your microservices (APIs, Frontends, Workers, etc.)  
- `backing-services/` – Placeholder for external dependencies (databases, caches, message queues)  
- `development-services/` – Development and testing utilities (e.g., mock services, Ollama, MCP)  
- `tmp/` – Runtime data directory (gitignored, for Redis data, Ollama models, etc.)
- `docker-compose.yml` – Base multi-container architecture setup  
- `docker-compose.development.yml` – Development environment overlay  

### Scaffolded Workspace Structure

After scaffolding, your new workspace will have:

- `.devcontainer/` – Copied from template
- `.github/` – Copied from `templates/.github/` to root level
  - `copilot-instructions.md`
  - `chatmodes/`, `instructions/`, `prompts/`
- `.vscode/` – Copied from `templates/.vscode/` to root level
- `config/`, `services/`, `backing-services/`, `development-services/`, `tmp/` – Same as template
- `docker-compose.yml`, `docker-compose.development.yml` – Copied from template  

---

## 🎯 Purpose

This template helps you:

- Set up a consistent multi-container structure for microservices projects  
- Clearly separate development and production environments  
- Support Copilot/Agent workflows with instructions and prompts directly in VS Code  
- Build microservices modularly (Services, Backing-Services, Dev-Services)  
- Use intelligent marker systems for automated service additions by AI agents

---

## 🔧 Key Features

### VS Code Multiple Containers Architecture

This template implements the [VS Code Multiple Containers pattern](https://code.visualstudio.com/remote/advancedcontainers/connect-multiple-containers), enabling you to:
- **Work across multiple services** – Each microservice runs in its own container with its own development environment
- **Switch contexts seamlessly** – Quickly switch between microservices using VS Code's Dev Container switcher
- **Shared infrastructure** – All services share common backing services (databases, caches) via Docker Compose networks
- **Independent technology stacks** – Each service can use different languages, frameworks, and tools

#### .devcontainer Structure

After adding your first microservices, your workspace structure will look like this:

```
📁 project-root
    📁 .git
    📁 .devcontainer
      📁 api-container
        📄 devcontainer.json
      📁 frontend-container
        📄 devcontainer.json
      📁 worker-container
        📄 devcontainer.json
    📁 services
      📁 api
        📄 (source code)
      📁 frontend
        📄 (source code)
      📁 worker
        📄 (source code)
    📄 docker-compose.yml
```

**How it works:**
- Each service has its own `.devcontainer/<service-name>-container/devcontainer.json` at the root level
- The service's `devcontainer.json` references its Docker Compose service and uses `workspaceFolder` to point to the service code
- Service code remains under `services/<service-name>/`
- All containers mount the **root folder** (which contains `.git`) to `/workspace`
- VS Code's **"Dev Containers: Reopen in Container"** command starts all containers and connects to one
- VS Code's **"Dev Containers: Switch Container"** command lets you switch between running containers
- Each container has its own extensions, settings, and development tools
- All containers share the same Docker network for inter-service communication

**Example complete workspace structure:**
```
📁 project-root
  📁 .git                          ← Git repository root
  📁 .devcontainer                 ← Dev container configs (at root)
    📁 api-container
      📄 devcontainer.json         ← service: "api", workspaceFolder: "/workspace/services/api"
    📁 frontend-container
      📄 devcontainer.json         ← service: "frontend", workspaceFolder: "/workspace/services/frontend"
    📁 worker-container
      📄 devcontainer.json         ← service: "worker", workspaceFolder: "/workspace/services/worker"
  📁 services                      ← Service source code
    📁 api                         ← Python API service
      📄 Dockerfile
      📄 requirements.txt
      📁 src
        📄 main.py
        📁 models
        📁 routes
        📁 tests
    📁 frontend                    ← Node.js/React frontend
      📄 Dockerfile
      📄 package.json
      📁 src
        📄 App.tsx
        📁 components
        📁 pages
        📁 tests
    📁 worker                      ← Go worker service
      📄 Dockerfile
      📄 go.mod
      📁 cmd
        📄 main.go
  📄 docker-compose.yml            ← All services mount root: ".:/workspace"
```

**Example `devcontainer.json` for the API service** (`.devcontainer/api-container/devcontainer.json`):
```json
{
  "name": "API Container",
  "dockerComposeFile": [
    "../../docker-compose.yml",
    "../../docker-compose.development.yml"
  ],
  "service": "api",
  "workspaceFolder": "/workspace/services/api",
  "shutdownAction": "none",
  "customizations": {
    "vscode": {
      "extensions": ["ms-python.python", "ms-python.vscode-pylance"]
    }
  }
}
```

**Key configuration details:**
- `dockerComposeFile`: Array of compose files that are merged in order
  - `docker-compose.yml` – Base configuration for all environments
  - `docker-compose.development.yml` – Development-specific overrides and additions
- `service`: Specifies which Docker Compose service to connect to (`api` in this example)
- `workspaceFolder`: Sets VS Code's working directory to the specific service folder
- `shutdownAction: "none"`: Keeps all containers running when switching between services

**How docker-compose.development.yml works:**
- **Extends base configuration**: Merges with `docker-compose.yml` using Docker Compose's native override behavior
- **Development overrides**: Adds development-specific configurations like volume mounts for live code reloading
- **Additional services**: Includes development-only services (e.g., Ollama, mock APIs, debugging tools)
- **Environment adjustments**: May expose additional ports, enable debug modes, or add development dependencies

**Example override in docker-compose.development.yml:**
```yaml
services:
  api:
    volumes:
      - .:/workspace  # Mount root folder for live code editing
    environment:
      - DEBUG=true    # Enable debug mode
    ports:
      - "5678:5678"   # Expose debugger port
```

This approach keeps production configuration clean in `docker-compose.yml` while `docker-compose.development.yml` provides the development experience without affecting production deployments.

### Docker Compose Marker System

The compose files use special markers that allow AI agents to programmatically insert services:
- `##<<BACKING_SERVICES>>` / `##</BACKING_SERVICES>` – For databases, caches, queues
- `##<<SERVICES>>` / `##<</SERVICES>>` – For application services
- `##<<DEVELOPMENT_SERVICES>>` / `##</DEVELOPMENT_SERVICES>` – For dev-only services

### Copilot Integration

The template provides a rich AI-assisted development experience:

**[Custom Instructions](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)** (`.github/copilot-instructions.md`):
- Define workspace conventions and coding standards
- Specify architectural patterns and best practices
- Guide Copilot's code generation behavior

**[Prompt Files](https://code.visualstudio.com/docs/copilot/customization/prompt-files)** (`.github/prompts/`):
- Reusable templates for common tasks
- Standardized patterns for adding services, APIs, tests
- Shareable across team members

**[Custom Chat Modes](https://code.visualstudio.com/docs/copilot/customization/custom-chat-modes)** (`.github/chatmodes/`):
- Specialized modes for specific workflows (e.g., "API Design", "Database Schema")
- Context-aware assistance for different development phases
- Project-specific expert personas

### Environment Configuration

- Centralized `.env.example` in `config/` directory
- Copy to `config/.env` for local development (gitignored)
- Referenced in both compose files via `env_file: [./config/.env]`

### Placeholder Structure

- `.gitkeep` files maintain empty directories in version control
- Ready for you to add your own services without cluttering the template

---

## 🚀 Workspace Initialization

Instead of directly cloning this repository, use this **two-phase scaffold process**.

### Quick Start (Agent Prompt)

Use this minimal prompt in Copilot Agent Chat (select a capable model such as "GPT-5 mini" or similar):

```text
Scaffold this empty workspace using the instructions at:
https://github.com/andrelohmann/codepilot-microservices-template/blob/main/INSTRUCTIONS.md

Follow ONLY what is written in that INSTRUCTIONS.md file.
- Do not infer or improvise missing rules from this prompt.
- Do not copy README.md or INSTRUCTIONS.md into this workspace.
- Do not bring over git history (.git/).
- Present merge suggestions for conflicting existing files instead of overwriting silently.
Provide a final summary of actions.
```

### What Happens:

**PHASE 1 - Exact Clone:**
✅ Perfect copy of all template files via git clone  
✅ No LLM interpretation or file corruption  
✅ Isolated in `.template-temp/` subdirectory  

**PHASE 2 - Intelligent Migration:**
✅ Copies files that should always be updated  
✅ Moves `templates/.github/` and `templates/.vscode/` to root  
✅ **Smart conflict detection** for existing files  
✅ **Merge suggestions** for configuration files  
✅ **Preserves your customizations** in existing files  
✅ Skips unwanted files (README, INSTRUCTIONS, .git)  

**PHASE 3 - Clean Workspace:**
✅ Removes temporary clone directory  
✅ Creates proper placeholder structure  
✅ Provides detailed migration report  

### Conflict Resolution Example:

If you already have a `.vscode/settings.json`, the agent will:

```
📄 CONFLICT DETECTED: .vscode/settings.json

🔵 Your existing version:
{
  "editor.formatOnSave": true,
  "myCustom.setting": "value"
}

🟢 Template version:
{
  "editor.formatOnSave": false,
  "docker.composeCommand": "docker compose",
  "copilot.enable": true
}

💡 Suggested merge:
{
  "editor.formatOnSave": true,           ← Preserved from your version
  "myCustom.setting": "value",           ← Preserved from your version
  "docker.composeCommand": "docker compose",  ← Added from template
  "copilot.enable": true                 ← Added from template
}

Accept this merge? (yes/no/edit)
```

---

## 📜 License

This project is licensed under the MIT License. See the `LICENSE` file in the repository root.

When scaffolding a new workspace:
- Do NOT overwrite an existing `LICENSE` unless explicitly instructed.
- If no license exists, copy the template `LICENSE` verbatim.


