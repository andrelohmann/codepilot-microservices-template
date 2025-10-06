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
Lines:     ─ │ ┌ ┐ └ ┘ ├ ┤ ┬ ┴ ┼
Heavy:     ═ ║ ╔ ╗ ╚ ╝
Arrows:    → ← ↑ ↓ ⇒ ⇐
Symbols:   ✓ ✗ ⚠ ● ○
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
