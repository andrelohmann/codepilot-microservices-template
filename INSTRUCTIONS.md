# INSTRUCTIONS.md — Microservices Workspace Scaffolding Instructions

> **Note:** This file is meant for an LLM / AI Agent (e.g., GitHub Copilot in VS Code) to fetch and follow.  
> The scaffold prompt in the README.md will refer to this file by URL.  
> The Agent should download this file, parse it, and execute the scaffolding process as described below.

---

## 🎯 Overview

These instructions guide AI agents to initialize a new microservices workspace using this template repository.

### Canonical Source Principle

Agents MUST treat this INSTRUCTIONS.md file as the **single source of truth**.

Do NOT:
- Infer additional behavior from the README prompt beyond fetching this file
- Invent or transform file contents
- Rename or reformat files unless explicitly described

Do:
- Follow steps exactly as ordered
- Pause on conflicts and request user decision when required
- Preserve original file bytes unless a merge is explicitly approved

### License Handling

- If a `LICENSE` file already exists in the target workspace: **do not overwrite**
- If no `LICENSE` exists: copy the template `LICENSE` file verbatim
- Never modify the text of the MIT License

## ⚠️ CRITICAL: Two-Phase Approach

This scaffolding uses a **two-phase process**:
1. **PHASE 1**: Clone the template to a temporary subdirectory (exact copy via git)
2. **PHASE 2**: Intelligently migrate files to the workspace root (with conflict detection)
3. **PHASE 3**: Cleanup and finalize

This approach ensures:
- ✅ **100% accurate file copies** (via git clone, no LLM interpretation)
- ✅ **Smart conflict resolution** (when workspace already has files)
- ✅ **No git history pollution** (clone is temporary and removed)

---

## 📋 PHASE 1: Clone Template

### Step 1.1: Clone to Temporary Location

```bash
git clone https://github.com/andrelohmann/codepilot-microservices-template.git .template-temp
```

**Important:**
- Clone to `.template-temp/` subdirectory in the current workspace
- Do NOT modify any files yet
- This is a temporary copy that will be removed later

### Step 1.2: Verify Clone

Confirm these directories exist in `.template-temp/`:
- `.devcontainer/`
- `templates/.github/`
- `templates/.vscode/`
- `config/`
- `docker-compose.yml`
- `docker-compose.development.yml`

---

## 📋 PHASE 2: Smart File Migration

### Step 2.1: Files to ALWAYS Copy (Overwrite)

These files should be copied from `.template-temp/` to workspace root, **overwriting** if they exist:

1. **Dev Container Configuration:**
   - `.template-temp/.devcontainer/devcontainer.json` → `.devcontainer/devcontainer.json`

2. **Docker Compose Files:**
   - `.template-temp/docker-compose.yml` → `docker-compose.yml`
   - `.template-temp/docker-compose.development.yml` → `docker-compose.development.yml`

3. **Configuration Template:**
   - `.template-temp/config/.env.example` → `config/.env.example`
   - **DO NOT** copy `config/.env` (if it exists in either location)

4. **Development Services:**
   - `.template-temp/development-services/` → `development-services/`
   - Preserve directory structure
   - Include `openai-api/entrypoint.sh` with executable permissions

5. **Gitignore:**
   - `.template-temp/.gitignore` → `.gitignore` (merge if exists, see Step 2.3)

### Step 2.2: Files to Move from templates/ to Root

These files are in `.template-temp/templates/` but should be placed at the workspace root:

1. **GitHub Copilot Configuration:**
   - `.template-temp/templates/.github/copilot-instructions.md` → `.github/copilot-instructions.md`
   - `.template-temp/templates/.github/chatmodes/` → `.github/chatmodes/`
   - `.template-temp/templates/.github/instructions/` → `.github/instructions/`
   - `.template-temp/templates/.github/prompts/` → `.github/prompts/`

2. **VS Code Configuration:**
   - `.template-temp/templates/.vscode/extensions.json` → `.vscode/extensions.json`
   - `.template-temp/templates/.vscode/settings.json` → `.vscode/settings.json`

### Step 2.3: Smart Conflict Detection & Resolution

For each file that already exists in the workspace, follow these rules:

#### A) Configuration Files (.json, .yml, .yaml, .md)

If the file exists:
1. **Read both versions** (existing workspace file + template file)
2. **Identify differences**:
   - Settings present only in workspace version
   - Settings present only in template version
   - Settings present in both (with different values)
3. **Suggest a merge** that:
   - Preserves all workspace-specific customizations
   - Adds new settings from the template
   - For conflicts, **prefer workspace values** unless template value is critical
4. **Present to user**:
   ```
   📄 CONFLICT: path/to/file.json
   
   🔵 Workspace version: { ... }
   🟢 Template version: { ... }
   💡 Suggested merge: { ... }
   
   Options:
   1. Accept merge
   2. Keep workspace version
   3. Use template version
   4. Edit manually
   ```

**Example merge logic for `.vscode/settings.json`:**
```json
{
  // From workspace (preserved)
  "editor.formatOnSave": true,
  "myProject.customSetting": "value",
  
  // From template (added)
  "docker.composeCommand": "docker compose",
  "copilot.enable": true,
  
  // Conflict (workspace wins)
  "editor.tabSize": 2  // workspace had 2, template had 4
}
```

#### B) Code/Script Files (.py, .js, .ts, .sh, etc.)

If the file exists:
1. **DO NOT overwrite**
2. **Inform the user**:
   ```
   ⚠️  Code file already exists: path/to/file.py
   
   Template provides a version of this file but will NOT overwrite.
   
   Options:
   1. Keep your version (recommended)
   2. Create backup: path/to/file.py.backup-YYYYMMDD
   3. View template version for reference
   ```

#### C) .gitignore Special Handling

If `.gitignore` exists:
1. **Merge both files**:
   - Keep all existing ignore patterns
   - Add new patterns from template that don't already exist
   - Remove duplicates
   - Preserve comments and section organization from workspace version
2. **Present merged version** for approval

### Step 2.4: Files to NEVER Copy

These files/directories should **NOT** be copied from `.template-temp/`:

1. **Git History:**
   - `.template-temp/.git/` (entire directory)

2. **Documentation:**
   - `.template-temp/README.md`
   - `.template-temp/INSTRUCTIONS.md`
   - `.template-temp/LICENSE`

3. **Template Folder Itself:**
   - `.template-temp/templates/` (only its contents are moved, not the folder)

4. **Temporary/Runtime Data:**
   - `.template-temp/tmp/` subdirectories (only copy `.gitkeep`)

---

## 📋 PHASE 3: Cleanup & Finalization

### Step 3.1: Create Placeholder Directories

Create these directories with `.gitkeep` files (if they don't already exist):

```
services/.gitkeep
backing-services/.gitkeep
development-services/.gitkeep (unless files were copied there)
tmp/.gitkeep
```

**Important:** Only create `.gitkeep`, do NOT create subdirectories like `tmp/.redis/` or `tmp/.ollama/`

### Step 3.2: Remove Temporary Clone

```bash
rm -rf .template-temp
```

**Verify** that `.template-temp/` is completely removed.

### Step 3.3: Verify Final Structure

The workspace should now have:

```
/ (workspace root)
├── .devcontainer/
│   └── devcontainer.json
├── .github/
│   ├── copilot-instructions.md
│   ├── chatmodes/
│   ├── instructions/
│   └── prompts/
├── .vscode/
│   ├── extensions.json
│   └── settings.json
├── config/
│   └── .env.example
├── services/
│   └── .gitkeep
├── backing-services/
│   └── .gitkeep
├── development-services/
│   └── openai-api/
│       └── entrypoint.sh
├── tmp/
│   └── .gitkeep
├── docker-compose.yml
├── docker-compose.development.yml
├── .gitignore
└── README.md (if it already existed, otherwise not present)
```

### Step 3.4: Provide Summary Report

Generate a comprehensive summary:

```
✅ Scaffolding Complete!

📊 Summary:
  ✅ Files copied: 15
  🔀 Files merged: 2
    - .vscode/settings.json (preserved your customizations)
    - .gitignore (combined both versions)
  ⏭️  Files skipped: 1
    - src/main.py (already exists, not overwritten)
  📁 Directories created: 4
    - services/ (with .gitkeep)
    - backing-services/ (with .gitkeep)
    - development-services/ (with .gitkeep)
    - tmp/ (with .gitkeep)

🔍 Review merged files:
  - .vscode/settings.json: Added docker.composeCommand, copilot.enable
  - .gitignore: Added tmp/, .env patterns

📝 Next steps:
  1. Review merged files above
  2. Copy config/.env.example to config/.env
  3. Edit config/.env with your values
  4. Run: docker-compose -f docker-compose.yml -f docker-compose.development.yml up -d
  5. Open workspace in Dev Container
```

---

## 🎯 Agent Execution Guidelines

### Execution Flow

1. **Execute PHASE 1 first** (git clone)
2. **Pause and report** clone status
3. **Ask user**: "Template cloned successfully. Proceed with file migration? (yes/no)"
4. **Execute PHASE 2** (smart migration with conflict detection)
5. **For each conflict**, present options and wait for user choice
6. **Execute PHASE 3** (cleanup and finalization)
7. **Present final summary**

### Error Handling

- If git clone fails: Report error, suggest checking network/repository URL
- If a file cannot be read: Skip it and report in summary
- If a merge conflict is too complex: Offer to create both files with `.workspace` and `.template` suffixes
- If cleanup fails: Report which files couldn't be removed

### Best Practices

- **Always show diffs** for conflicting files
- **Default to preserving** user's existing files
- **Be transparent** about what's being changed
- **Provide undo information** (e.g., backup locations)
- **Don't assume** - ask when uncertain

---

## 🔍 Verification Checklist

After completion, verify:

- [ ] `.template-temp/` directory is completely removed
- [ ] All template files are in correct locations
- [ ] No `.git/` directory from template exists
- [ ] `config/.env.example` exists but `config/.env` does not (unless user created it)
- [ ] Placeholder `.gitkeep` files exist
- [ ] No `README.md` or `INSTRUCTIONS.md` from template (unless workspace was empty)
- [ ] User's existing files are preserved or merged appropriately
- [ ] All conflicts have been resolved or documented

---

## 📚 Reference: Docker Compose Marker System

The template uses these markers in `docker-compose.yml` and `docker-compose.development.yml`:

```yaml
##<<BACKING_SERVICES>>
# Add databases, caches, queues here
##</BACKING_SERVICES>>

##<<SERVICES>>
# Add your microservices here
##</SERVICES>>

##<<DEVELOPMENT_SERVICES>>
# Add development tools here (only in docker-compose.development.yml)
##</DEVELOPMENT_SERVICES>>
```

When adding new services in the future, place them between the appropriate markers.
