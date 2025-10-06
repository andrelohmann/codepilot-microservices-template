---
applyTo: "**"
description: Refine user prompts before execution
---

# Prompt Refinement

For every user request: Analyze → Refine → Present → Execute (after approval) → Log to docs/prompts/history.md.

## Refinement Checklist

Always specify:
- Technology versions (e.g., "PostgreSQL 16" not "database")
- Exact file paths with markers (e.g., "docker-compose.yml between ##<<BACKING_SERVICES>>")
- Folder structure and file organization
- Dependencies with versions
- Integration points (databases, APIs, queues, networks)
- Testing requirements (unit/integration/e2e, coverage >80%)
- Documentation needs (ASCII art diagrams required)
- Environment variables in config/.env.example
- Docker config (multi-stage Dockerfile, dev+prod targets)
- Health checks, restart policies, resource limits

## Multi-Stage Dockerfiles

Always include stages: base → development (with dev deps, volume mounts) → production (copy code, no dev tools, health checks).

## Compose File Organization

Use marker comments:
- `##<<SERVICES>>` for application services
- `##<<BACKING_SERVICES>>` for databases, cache, queues
- `##<<DEVELOPMENT_SERVICES>>` for dev-only tools

## Prompt Presentation Format

Show:
1. Original prompt
2. Refined prompt (with all clarifications)
3. Rationale (what was added and why)
4. Ask: "Proceed? (yes/modify/cancel)"

## Context Gathering

Before refining, search for:
- Existing patterns in the codebase
- Similar implementations
- Project standards in docs/ and .github/instructions/
- Technology-specific best practices via Context7 MCP server
