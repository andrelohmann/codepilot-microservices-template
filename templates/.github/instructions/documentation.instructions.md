---
applyTo: "docs/**/*.md"
description: Documentation and ASCII art diagram standards
---

# Documentation Standards

## Location

All documentation in `docs/` by category:
- `architecture/` - System architecture, data flows
- `adr/` - Architecture Decision Records
- `services/` - Service-specific docs
- `operations/` - Deployment, monitoring
- `getting-started/` - Setup guides

## ASCII Art Diagrams

Every doc MUST include ASCII diagrams using Unicode box-drawing.

### Characters

```
Basic Characters:
─  Horizontal line          (U+2500)
│  Vertical line            (U+2502)
┌  Top-left corner          (U+250C)
┐  Top-right corner         (U+2510)
└  Bottom-left corner       (U+2514)
┘  Bottom-right corner      (U+2518)
├  Left T-junction          (U+251C)
┤  Right T-junction         (U+2524)
┬  Top T-junction           (U+252C)
┴  Bottom T-junction        (U+2534)
┼  Cross junction           (U+253C)

Heavy Lines (for emphasis):
═  Heavy horizontal         (U+2550)
║  Heavy vertical           (U+2551)
╔  Heavy top-left           (U+2554)
╗  Heavy top-right          (U+2557)
╚  Heavy bottom-left        (U+255A)
╝  Heavy bottom-right       (U+255D)

Arrows:
→  Right arrow              (U+2192)
←  Left arrow               (U+2190)
↑  Up arrow                 (U+2191)
↓  Down arrow               (U+2193)
↔  Left-right arrow         (U+2194)
↕  Up-down arrow            (U+2195)
⇒  Double right arrow       (U+21D2)
⇐  Double left arrow        (U+21D0)

Symbols:
▼  Down triangle            (U+25BC)
▲  Up triangle              (U+25B2)
◆  Diamond                  (U+25C6)
●  Filled circle            (U+25CF)
○  Empty circle             (U+25CB)
✓  Check mark               (U+2713)
✗  Cross mark               (U+2717)
⚠  Warning sign             (U+26A0)
```

### Components

```
Standard:   ┌─────────┐
            │ Service │
            └─────────┘

Critical:   ╔═════════╗
            ║  Secure ║
            ╚═════════╝

Optional:   ┌ ─ ─ ─ ─ ┐
             Optional
            └ ─ ─ ─ ─ ┘
```

### Connections

```
Simple:   A ──→ B
Labeled:  A ──HTTP──→ B
Critical: A ══════⇒ B
Optional: A ─ ─ ─→ B
```

## Design Rules

1. Simple flows, avoid complexity
2. Align boxes, use whitespace
3. Label all components and connections
4. Top-to-bottom for requests, left-to-right for processes

## Anti-Patterns

- ❌ Images instead of ASCII (except UI mockups)
- ❌ Plain text boxes: `+---+` (use `┌───┐`)
- ❌ Unlabeled connections
- ❌ Missing diagrams

## Task List Generation - 3S Principle

When generating task lists for implementation work, ALWAYS follow the **3S Principle**:

### The 3S Principle

1. **Simple** - Each task should be straightforward and easy to understand
2. **Specific** - Clear, concrete actions with no ambiguity  
3. **Short** - Small, manageable chunks that can be completed quickly

### Good Examples (✅ Follow 3S)

```
✅ "Create docs/tech-stacks/apis/actix.md file"
✅ "Add Actix entry to apis/README.md comparison table"
✅ "Update NAMING-CONVENTIONS.md with actix identifier"
✅ "Add health check endpoint to API service"
✅ "Configure Redis connection in docker-compose.yml"
```

### Bad Examples (❌ Violate 3S)

```
❌ "Update all documentation" (not specific)
❌ "Create comprehensive documentation with examples, configurations, testing, deployment, monitoring, and best practices" (not simple or short)
❌ "Make improvements" (not specific)
❌ "Fix everything" (not specific or simple)
❌ "Implement the entire authentication system" (not short - should be broken down)
```

### Application Rules

- **Break down complex work** into multiple simple tasks
- Each task must have **clear completion criteria**
- **Prefer 10 simple tasks** over 1 complex task
- Each task should be completable in **5-15 minutes**
- Tasks should be **independently verifiable**
- Use **action verbs**: Create, Update, Add, Configure, Test, Deploy

