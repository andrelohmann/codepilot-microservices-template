## INSTRUCTIONS.md — Minimal Scaffold Procedure

Purpose: Let an AI agent (or you) copy this template into an existing empty (or partially initialized) workspace with the fewest possible steps and zero unintended file mutation.

Only do exactly what is written here. Do not invent extra steps.

---

### 0. Rules (Read First)
1. Never alter file contents while copying (byte‑for‑byte).
2. Never copy the template `.git/` history.
3. If a `LICENSE` already exists in the workspace: keep it. Otherwise copy the template `LICENSE` verbatim.
4. Never copy `config/.env` (only copy `.env.example`).
5. Do not create runtime subfolders (e.g. `tmp/.redis/`, `tmp/.ollama/`). Only create `tmp/.gitkeep` if missing.
6. Do not overwrite existing “code” files (source or scripts) without explicit user approval.
7. When unsure, stop and ask.

---

### 1. Clone (temporary)
Create a temporary clone inside the current workspace (root):
```
git clone https://github.com/andrelohmann/codepilot-microservices-template.git tmp-clone
```

You should now have `tmp-clone/` containing the template.

---

### 2. Copy Required Files
Copy (or merge) from `tmp-clone/` into the workspace root. Preserve folder structure.

Always create target directories if absent.

Copy / relocate list:
- `.devcontainer/devcontainer.json`
- `docker-compose.yml`
- `docker-compose.development.yml`
- `config/.env.example` (skip `config/.env` if present in either source or destination)
- Everything under `tmp-clone/templates/.github/` → `.github/` (preserve subfolders: `chatmodes/`, `instructions/`, `prompts/`)
- Everything under `tmp-clone/templates/.vscode/` → `.vscode/`
- Entire `development-services/` directory (preserve executable bits on scripts like `entrypoint.sh`)
- `.gitignore` (merge if already exists; see Merge Guidance below)
- `LICENSE` (only if missing in destination)

Placeholders (create if missing):
- `services/.gitkeep`
- `backing-services/.gitkeep`
- `tmp/.gitkeep`

Do NOT copy:
- `tmp-clone/.git/`
- `tmp-clone/README.md`
- `tmp-clone/INSTRUCTIONS.md`
- `tmp-clone/tmp/*` (other than a `.gitkeep` if you choose)

---

### 3. Compare & Recommend Merges
For every file that already exists in the destination path before copying:

1. If it is configuration / structured text (`.json`, `.yml`, `.yaml`, `.md`, `.gitignore`):
   - Read both versions.
   - Produce a concise diff summary (added / removed / changed keys or lines).
   - Suggest a merged result that keeps all destination‑only entries and adds missing template entries. Destination values win on conflict unless user overrides.
   - Ask user: [A] Accept merge, [B] Keep destination, [C] Use template, [D] Show full diff.

2. If it is code / scripts (`.py`, `.ts`, `.js`, `.sh`, etc.):
   - Do not overwrite automatically.
   - Offer options: show template version, keep existing, or save template as `filename.template` alongside.

3. `.gitignore` special case:
   - Combine unique lines preserving original order of the destination.
   - Append any new template patterns at the end (avoid duplicates).
   - Present merged version for approval.

4. If file identical (byte comparison): skip reporting.

After processing all conflicts, perform the approved copies / writes.

---

### 4. Cleanup
Remove the temporary clone directory when finished:
```
rm -rf tmp-clone
```
Confirm it is gone.

---

### 5. Minimal Completion Checklist
- [ ] `docker-compose.yml` present
- [ ] `docker-compose.development.yml` present
- [ ] `.devcontainer/devcontainer.json` present
- [ ] `.github/` (with `instructions/`, `prompts/`, etc.)
- [ ] `.vscode/settings.json` & `extensions.json`
- [ ] `config/.env.example` (no unintended `config/.env` copied)
- [ ] Placeholder dirs: `services/`, `backing-services/`, `tmp/` each with `.gitkeep`
- [ ] No `tmp-clone/` remains
- [ ] Existing user files preserved or merged per choices

---

### 6. (Optional) Markers Reference
`docker-compose.yml` and `docker-compose.development.yml` contain insertion markers:
```
##<<BACKING_SERVICES>>
##</BACKING_SERVICES>>
##<<SERVICES>>
##</SERVICES>>
##<<DEVELOPMENT_SERVICES>>
##</DEVELOPMENT_SERVICES>>
```
Add new services strictly between matching marker pairs.

---

### 7. Final Report Format (Example)
```
Scaffold Summary
Copied: 11 files
Merged: .gitignore, .vscode/settings.json
Skipped (already existed, left untouched): 2 (scripts)
Added placeholders: services/.gitkeep, backing-services/.gitkeep, tmp/.gitkeep
Temp cleaned: yes
Next: create config/.env from config/.env.example and start compose.
```

Done.
