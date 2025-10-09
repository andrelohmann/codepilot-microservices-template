# Documentation Standards

This document defines mandatory standards for all documentation in this workspace.

---

## 📍 Documentation Location

**All documentation MUST be placed in the `docs/` folder.**

Never scatter documentation across the codebase. Centralize in `docs/` for discoverability.

### Folder Structure

```
docs/
├── architecture/        # System-wide architecture
├── adr/                 # Architecture Decision Records
├── services/            # Service-specific docs
├── operations/          # Deployment, monitoring, troubleshooting
├── getting-started/     # Onboarding guides
└── prompts/             # Prompt history and templates
```

---

## 🎨 ASCII Art Requirements

**Every documentation file MUST include ASCII art diagrams.**

### Why ASCII Art?

- **Universal:** Renders everywhere (terminal, GitHub, VS Code, plain text)
- **Version Control Friendly:** Clean diffs, no binary blobs
- **Lightweight:** No external tools required
- **Fast:** Immediate rendering, no image loading
- **Accessible:** Screen reader compatible with proper descriptions

### Mandatory Diagrams

Each documentation type requires specific diagrams:

#### Architecture Documentation
- System overview diagram (all components and connections)
- Data flow diagrams (request/response sequences)
- Network topology (Docker networks, ports, boundaries)

#### Service Documentation
- Internal architecture (layers, modules)
- External dependencies (databases, APIs, queues)
- Request processing flow
- Testing architecture

#### ADRs (Architecture Decision Records)
- Context diagram (problem space)
- Decision diagram (chosen solution)
- Consequences diagram (positive/negative impacts)

#### Operational Documentation
- Deployment pipeline flowchart
- Monitoring architecture
- Backup/restore process
- Troubleshooting decision trees

#### Getting Started Guides
- Setup workflow
- File structure diagrams
- Development cycle flowcharts

---

## 📐 ASCII Art Conventions

### Box Drawing Characters

Use Unicode box-drawing characters for clean, professional diagrams:

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

### Component Representations

```
┌─────────────────┐
│    Service      │  ← Standard service/component box
└─────────────────┘

┌─────────────────┐
│   Database      │  ← Data store (same format)
└─────────────────┘

╔═════════════════╗
║  Critical       ║  ← Heavy border for emphasis (security, production)
╚═════════════════╝

┌ ─ ─ ─ ─ ─ ─ ─ ─ ┐
  Optional/Dashed    ← Dashed lines for optional components
└ ─ ─ ─ ─ ─ ─ ─ ─ ┘
```

### Connection Types

```
Service A ──→ Service B        ← Simple connection

Service A ──HTTP──→ Service B  ← Labeled protocol

Service A ══════⇒ Service B    ← Heavy/critical path

Service A ─ ─ ─→ Service B     ← Optional/conditional

     ↓
   Request                      ← Vertical flow
     ↓
```

### Network Boundaries

```
════════════════════════════════  ← Network boundary (production)

--------------------------------  ← Network boundary (development)

┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈┈  ← Dotted boundary (logical grouping)
```

---

## 📝 Complete Example: Simple Service Architecture

```markdown
# API Service Architecture

## Overview

The API service is a FastAPI-based REST API that handles user management.

## System Context

┌──────────────────────────────────────────────────────────────────┐
│                        System Boundary                           │
│                                                                   │
│  ┌──────────┐       ┌──────────┐       ┌──────────────┐        │
│  │  Client  │──────→│   API    │──────→│  PostgreSQL  │        │
│  │ (Browser)│ HTTP  │ Service  │  SQL  │  (Database)  │        │
│  └──────────┘       └────┬─────┘       └──────────────┘        │
│                           │                                       │
│                           │ Redis Protocol                        │
│                           ↓                                       │
│                     ┌──────────┐                                 │
│                     │  Redis   │                                 │
│                     │ (Cache)  │                                 │
│                     └──────────┘                                 │
└──────────────────────────────────────────────────────────────────┘
```

## Internal Architecture

```
┌─────────────────────────────────────────────────────────────────┐
│                         API Service                             │
│                                                                  │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                    Router Layer                           │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │  │
│  │  │  /api/users  │  │  /api/auth   │  │  /api/health │   │  │
│  │  └──────┬───────┘  └──────┬───────┘  └──────────────┘   │  │
│  └─────────┼─────────────────┼──────────────────────────────┘  │
│            │                 │                                   │
│            ↓                 ↓                                   │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                   Business Logic Layer                    │  │
│  │  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐   │  │
│  │  │ UserService  │  │  AuthService │  │ EmailService │   │  │
│  │  └──────┬───────┘  └──────┬───────┘  └──────────────┘   │  │
│  └─────────┼─────────────────┼──────────────────────────────┘  │
│            │                 │                                   │
│            ↓                 ↓                                   │
│  ┌───────────────────────────────────────────────────────────┐  │
│  │                  Persistence Layer                        │  │
│  │  ┌──────────────────┐       ┌──────────────────┐         │  │
│  │  │ UserRepository   │       │  AuthRepository  │         │  │
│  │  │  (SQLAlchemy)    │       │   (Redis)        │         │  │
│  │  └────────┬─────────┘       └──────┬───────────┘         │  │
│  └───────────┼──────────────────────────┼───────────────────┘  │
│              │                          │                       │
└──────────────┼──────────────────────────┼───────────────────────┘
               │                          │
               ↓                          ↓
         ┌──────────┐              ┌──────────┐
         │PostgreSQL│              │  Redis   │
         └──────────┘              └──────────┘
```

## Request Flow

```
Client Request:
    │
    │ POST /api/users
    ↓
┌──────────────────┐
│   API Gateway    │  ← Authentication middleware
└────────┬─────────┘
         │
         ↓
┌──────────────────┐
│  Users Router    │  ← Parse request, validate input
└────────┬─────────┘
         │
         ↓
┌──────────────────┐
│   User Service   │  ← Business logic, validation
└────────┬─────────┘
         │
         ├────────→ Check cache (Redis)
         │              │
         │              ↓
         │          Cache hit? → Return cached
         │              │
         │              ↓ Cache miss
         │
         ↓
┌──────────────────┐
│ UserRepository   │  ← Query database
└────────┬─────────┘
         │
         ↓
┌──────────────────┐
│   PostgreSQL     │  ← Execute SQL
└────────┬─────────┘
         │
         ↓
      Response
         │
         ├────────→ Update cache (Redis)
         │
         ↓
    Return to client
```

---

## 🎯 Diagram Design Principles

### 1. Clarity Over Complexity

✅ **Good:**
```
API → Database
```

❌ **Bad:**
```
┌─────────────────────────────────────────────────────────────┐
│  ┌──────┐        ┌──────┐        ┌──────┐        ┌──────┐  │
│  │  A   │───────→│  B   │───────→│  C   │───────→│  D   │  │
│  └──────┘        └──────┘        └──────┘        └──────┘  │
└─────────────────────────────────────────────────────────────┘
```
(Too many unnecessary boxes for a simple flow)

### 2. Consistent Spacing

- Use consistent widths for similar components
- Align boxes vertically or horizontally
- Keep adequate spacing between elements
- Use whitespace to group related components

### 3. Clear Labels

- Label all connections with protocols (HTTP, SQL, AMQP)
- Name all components descriptively
- Add annotations for complex flows
- Use comments to explain non-obvious relationships

### 4. Layer Indication

```
┌───────────────────────────────────────────────────────┐
│               Presentation Layer                      │  ← Layer name
└───────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────┐
│               Business Logic Layer                    │
└───────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────┐
│               Data Access Layer                       │
└───────────────────────────────────────────────────────┘
```

### 5. Direction of Flow

- Top to bottom for request flows
- Left to right for process flows
- Use arrows consistently (→ for unidirectional, ↔ for bidirectional)

---

## 🔄 Update Requirements

### When to Update Diagrams

Update diagrams immediately when:
- Adding new services
- Changing communication protocols
- Modifying data flows
- Altering network topology
- Updating dependencies

### Review Cycle

- Review diagrams during code reviews
- Verify accuracy during sprint planning
- Update as part of architectural changes
- Include in documentation PRs

---

## ✅ Diagram Checklist

Before committing documentation with diagrams:

- [ ] All components are clearly labeled
- [ ] Connections show protocols/methods
- [ ] Direction of data flow is obvious
- [ ] Network boundaries are marked
- [ ] Legend is provided for complex diagrams
- [ ] Spacing is consistent
- [ ] Box drawing characters render correctly
- [ ] Diagram matches actual implementation
- [ ] Comments explain non-obvious elements

---

## 🛠 Tools & Tips

### Creating ASCII Art

**Manual:**
- Draw in monospace text editor (VS Code, vim, nano)
- Use Unicode input methods for box characters
- Test rendering in multiple environments

**Assisted:**
- asciiflow.com (web-based drawing tool)
- boxes (command-line tool for drawing)
- ditaa (convert ASCII to PNG for presentations)

### Testing Rendering

View your diagrams in:
- VS Code preview
- GitHub markdown preview
- Terminal (`cat docs/file.md`)
- Web browsers

Ensure consistent monospace font rendering.

---

## 📚 Examples Repository

Find more examples in:
- `docs/architecture/overview.md` — Full system diagrams
- `docs/services/*.md` — Service-specific examples
- `docs/adr/*.md` — Decision record diagrams
- `docs/prompts/history.md` — Workflow diagrams

---

## 🚫 What NOT to Do

❌ **Don't use images instead of ASCII art**
- Exceptions: Complex UI mockups, photos, external tool outputs

❌ **Don't create diagrams without labels**
- Every box and arrow must be clearly labeled

❌ **Don't skip diagrams**
- Every doc needs at least one diagram

❌ **Don't use plain text boxes**
```
Bad:
+----------+
| Service  |
+----------+
```

Use proper Unicode box drawing:
```
Good:
┌──────────┐
│ Service  │
└──────────┘
```

❌ **Don't make diagrams too complex**
- Break complex systems into multiple simpler diagrams
- Use layering to show different levels of detail

---

## Summary

**Remember:**
1. All docs in `docs/` folder
2. Every doc MUST have ASCII art diagrams
3. Use proper Unicode box-drawing characters
4. Label everything clearly
5. Keep diagrams simple and focused
6. Update diagrams when code changes
7. Review diagrams in PRs

**The goal:** Make architecture and flows immediately understandable through clear, version-controlled, universally-renderable diagrams.
