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

Instead of directly cloning this repository, use it as an **external scaffold template**.  

### Step-by-Step:

1. **Create a new empty directory** for your project
2. **Open it in VS Code**
3. **Open the Agent Mode Chat** (Copilot) and **select the "Grok Code Fast 1" model**
4. **Run the following prompt:**

```text
Scaffold a new microservices workspace by fetching and following the instructions from:
https://github.com/andrelohmann/codepilot-microservices-template/blob/main/INSTRUCTIONS.md

CRITICAL instructions:
- Fetch the INSTRUCTIONS.md file from the URL above
- Follow ALL directives exactly as specified
- Copy all template files EXACTLY AS-IS without any modifications
- Do NOT edit, rename, or modify any file contents
- Do NOT use git clone - fetch files individually via HTTP/HTTPS
- Use raw GitHub URLs: https://raw.githubusercontent.com/andrelohmann/copilot-microservices-template/main/{filepath}
- Copy file contents byte-for-byte without interpretation
- Preserve exact filenames, directory structure, and file permissions
- Do NOT overwrite README.md if it already exists
- Do NOT copy INSTRUCTIONS.md itself to the new workspace
- Create placeholders (.gitkeep) for service directories
- Set up the docker-compose files with marker system for future service additions
- Create config/.env.example but NOT config/.env
- After completion, provide a comprehensive list of all created files and directories

Execute this scaffold in a single operation without asking for confirmation unless there are conflicts.
```

### What Happens Next:

The agent will:
✅ Create the complete directory structure  
✅ Copy Copilot configurations from `templates/.github/` to `.github/` in your workspace  
✅ Copy VS Code settings from `templates/.vscode/` to `.vscode/` in your workspace  
✅ Copy all other configuration files (`config/`, `.devcontainer/`, etc.)  
✅ Set up Docker Compose files with intelligent markers  
✅ Copy development services (like Ollama/OpenAI API)  
✅ Create placeholder directories for your services  
✅ Preserve your existing README.md (if any)  
✅ Provide a complete summary of created files  

### Result:

Your workspace will be a **near 100% copy** of this template, ready for you to start adding your own microservices following the Multi-Container pattern!

---

## 📖 Next Steps

After scaffolding your workspace:

1. **Configure environment variables:**
   ```bash
   cp config/.env.example config/.env
   # Edit config/.env with your actual values
   ```

2. **Start the development environment:**
   ```bash
   docker-compose -f docker-compose.yml -f docker-compose.development.yml up -d
   ```

3. **Open in VS Code Dev Container:**
   - Use the Command Palette (`Ctrl+Shift+P` / `Cmd+Shift+P`)
   - Select "Dev Containers: Open Workspace in Container"

4. **Add your first service:**
   - Use Copilot to generate service structure
   - Services are automatically added between the markers in docker-compose files

---

## 📚 Documentation

### Template Documentation
- **INSTRUCTIONS.md** – Complete scaffolding instructions for agents (not copied to new workspaces)
- **templates/.github/copilot-instructions.md** – Copilot behavior and workspace conventions (copied to `.github/` in new workspace)
- **templates/.github/instructions/** – Additional instruction files like compose-guardrails (copied to `.github/instructions/`)
- **templates/.github/prompts/** – Reusable prompt templates for common tasks (copied to `.github/prompts/`)
- **templates/.github/chatmodes/** – Custom chat modes for specific development workflows (copied to `.github/chatmodes/`)
- **templates/.vscode/** – VS Code extensions and settings (copied to `.vscode/` in new workspace)

### VS Code Documentation
- **[Connect to Multiple Containers](https://code.visualstudio.com/remote/advancedcontainers/connect-multiple-containers)** – Learn how VS Code manages multiple dev containers
- **[Custom Instructions for Copilot](https://code.visualstudio.com/docs/copilot/customization/custom-instructions)** – Configure workspace-specific Copilot behavior
- **[Prompt Files](https://code.visualstudio.com/docs/copilot/customization/prompt-files)** – Create and use reusable prompt templates
- **[Custom Chat Modes](https://code.visualstudio.com/docs/copilot/customization/custom-chat-modes)** – Build specialized chat modes for your workflow

---

## 🤝 Contributing

This is a template repository. To contribute:

1. Fork the repository
2. Create a feature branch
3. Make your changes
4. Test the scaffolding process in a new empty directory
5. Submit a pull request

---

## 📜 License

[MIT License](./LICENSE)

