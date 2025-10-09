# Adding New Technology Stacks

This guide explains how to extend the template with new technology stacks.

## Overview

To add a new technology stack, you need to:

1. Update documentation (this tech-stacks folder)
2. Create instruction file for GitHub Copilot
3. Create scaffold prompt for service generation
4. Test with a sample service
5. Update related documentation

---

## 🎯 Task List Principle

**When generating task lists during the implementation process, always follow the 3S Principle:**

### The 3S Principle

1. **Simple** - Each task should be straightforward and easy to understand
2. **Specific** - Clear, concrete actions with no ambiguity
3. **Short** - Small, manageable chunks that can be completed quickly

**Example of good 3S tasks:**
- ✅ "Create docs/tech-stacks/apis/actix.md file"
- ✅ "Add Actix entry to apis/README.md comparison table"
- ✅ "Update NAMING-CONVENTIONS.md with actix identifier"

**Example of bad tasks:**
- ❌ "Update all documentation" (not specific)
- ❌ "Create comprehensive documentation with all examples, configurations, testing strategies, deployment guides, monitoring setup, and best practices" (not simple or short)
- ❌ "Make improvements" (not specific)

**Application:**
- Break down complex work into multiple simple tasks
- Each task should have a clear completion criteria
- Prefer 10 simple tasks over 1 complex task

---

## Step-by-Step Process

### Step 1: Update Tech Stack Documentation

**1.1 Update Quick Reference**

Edit `docs/tech-stacks/README.md`:
- Add new technology to the appropriate service type table
- Add to Quick Reference section
- Add to Technology Quick Picks table

**1.2 Update Naming Conventions**

Edit `docs/tech-stacks/NAMING-CONVENTIONS.md`:
- Add technology identifier to the list
- Add naming examples
- Add to instruction file mapping

**1.3 Update Decision Tree**

Edit `docs/tech-stacks/DECISION-TREE.md`:
- Add to decision tree diagram
- Add to Quick Decision Guide
- Add to Decision Criteria tables

**1.4 Create Technology-Specific Documentation**

Create `docs/tech-stacks/{service-type}/{tech}.md`:

```markdown
# {Technology Name} - {Service Type}

## Overview

Brief description of the technology and when to use it.

## Naming Convention

`{type}-{tech}-{purpose}`

**Examples:**
- `{type}-{tech}-example1`
- `{type}-{tech}-example2`

## Technology Stack

### Core Technologies
- Technology 1.x
- Framework 2.x
- Library 3.x

### Features
- Feature 1
- Feature 2
- Feature 3

## Ideal Use Cases

1. **Use Case 1**
   - Scenario description
   - Why this tech is perfect

2. **Use Case 2**
   - Scenario description
   - Why this tech is perfect

## Project Structure

\`\`\`
services/{type}-{tech}-{purpose}/
├── src/
├── tests/
├── Dockerfile
└── package.json (or requirements.txt, go.mod, etc.)
\`\`\`

## Testing

- Test framework
- Test types (unit, integration, E2E)
- Test commands

## Deployment

- Build process
- Docker configuration
- Environment variables

## See Also

- [Service Type Overview](./README.md)
- [Naming Conventions](../NAMING-CONVENTIONS.md)
- [Decision Tree](../DECISION-TREE.md)
```

**1.5 Update Service Type Overview**

Edit `docs/tech-stacks/{service-type}/README.md`:
- Add new technology to comparison table
- Add to "When to Use" section
- Add link to detailed documentation

---

### Step 2: Create Instruction File

Create `templates/.github/instructions/service-{type}-{tech}.instructions.md`

**Template:**

```markdown
---
applyTo:
  - "services/{type}-{tech}-*/**"
  - ".devcontainer/{type}-{tech}-*-container/**"
description: "{Technology Name} {Service Type} development patterns and best practices"
---

# {Technology Name} {Service Type} Instructions

## Technology Stack

- {Technology} {Version}+
- {Framework} {Version}+
- {Key Libraries}

## Project Structure

\`\`\`
services/{type}-{tech}-{purpose}/
├── src/
│   ├── main.{ext}
│   └── ...
├── tests/
│   ├── unit/
│   └── integration/
├── Dockerfile
├── .dockerignore
├── {config-file}
├── .env.example
└── README.md
\`\`\`

## Core Patterns

### Pattern 1: {Pattern Name}

Description and code example.

### Pattern 2: {Pattern Name}

Description and code example.

## Testing Standards

### Unit Tests
- Test framework
- Coverage requirements
- Example

### Integration Tests
- Test approach
- Example

## Environment Configuration

Required environment variables:
- `VAR_1`: Description
- `VAR_2`: Description

## Docker Configuration

### Multi-Stage Build
- Stage 1: Dependencies
- Stage 2: Build
- Stage 3: Production

### Health Check
- Endpoint
- Interval
- Timeout

## Anti-Patterns

### ❌ Anti-Pattern 1
Description of what NOT to do.

### ✅ Correct Approach
Description of what TO do.

## Dependencies

### Production
- dependency1
- dependency2

### Development
- dev-dependency1
- dev-dependency2

## See Also

- [{Service Type} Overview](../../docs/tech-stacks/{service-type}/README.md)
- [{Technology} Documentation](../../docs/tech-stacks/{service-type}/{tech}.md)
```

---

### Step 3: Create Scaffold Prompt

Create `templates/.github/prompts/scaffold-{type}-{tech}-service.md`

**Template Structure:**

1. **Service Specification** (Interactive form)
2. **Architecture Overview** (ASCII diagram)
3. **Directory Structure** (Complete file tree)
4. **Implementation Steps** (18-20 detailed steps)
5. **Core Files** (Complete code examples)
6. **Docker Configuration** (Dockerfile + docker-compose)
7. **DevContainer Setup** (devcontainer.json)
8. **README Template** (Service documentation)
9. **Completion Checklist** (Verification steps)

**Key Sections:**

```markdown
# Scaffold {Technology} {Service Type} Service

## Service Specification

Please provide the following information:

**Service Name:** `{type}-{tech}-{purpose}`
**Purpose:** [Brief description]
**Key Features:**
- [ ] Feature 1
- [ ] Feature 2

---

## Architecture Overview

\`\`\`
[ASCII Architecture Diagram]
\`\`\`

---

## Directory Structure

\`\`\`
services/{type}-{tech}-{purpose}/
├── Complete file tree
\`\`\`

---

## Implementation Steps

### Step 1: Initialize Project

Details and commands.

### Step 2-20: [Continue with detailed steps]

---

## Core Files

### {config-file}

\`\`\`{language}
[Complete configuration]
\`\`\`

### src/main.{ext}

\`\`\`{language}
[Complete implementation]
\`\`\`

[Continue with all core files]

---

## Docker Configuration

### Dockerfile

\`\`\`dockerfile
[Multi-stage production-ready Dockerfile]
\`\`\`

### docker-compose.yml entry

\`\`\`yaml
[Service definition]
\`\`\`

---

## DevContainer Configuration

\`\`\`json
[Complete devcontainer.json]
\`\`\`

---

## README.md Template

\`\`\`markdown
[Complete README template]
\`\`\`

---

## Completion Checklist

- [ ] Service runs locally
- [ ] Tests pass
- [ ] Docker build successful
- [ ] DevContainer works
- [ ] Documentation complete
- [ ] Environment variables configured
- [ ] Health check responds
- [ ] Logging configured
- [ ] Metrics exposed
- [ ] Production-ready
```

---

### Step 4: Test with Sample Service

**4.1 Create Test Service**

```bash
# Use GitHub Copilot to scaffold the service
# Verify instruction file is applied
```

**4.2 Validate Generated Code**

- [ ] Service structure matches template
- [ ] All files are created
- [ ] Configuration is correct
- [ ] Dependencies are installed

**4.3 Test Functionality**

```bash
# Build Docker image
docker build -t {type}-{tech}-test services/{type}-{tech}-test/

# Run service
docker-compose up {type}-{tech}-test

# Run tests
docker-compose exec {type}-{tech}-test {test-command}

# Verify health check
curl http://localhost:{port}/health
```

**4.4 Test DevContainer**

- [ ] Open in DevContainer
- [ ] Extensions are installed
- [ ] Environment is configured
- [ ] Can run and debug

---

### Step 5: Update Related Documentation

**5.1 Update Main README**

Add to the relevant service type README.md (e.g., `docs/tech-stacks/apis/README.md`).

**5.2 Update Getting Started Guides**

Add examples in `docs/getting-started/`.

**5.3 Update Component Catalog**

Add to `docs/MICROSERVICES-COMPONENT-CATALOG.md` if needed.

**5.4 Create ADR (Architecture Decision Record)**

If introducing new patterns, create `docs/adr/NNN-{technology}-stack.md`:

```markdown
# ADR NNN: Add {Technology} Stack

## Status

Accepted

## Context

Why we're adding this technology stack.

## Decision

We will support {Technology} for {Service Type}.

## Consequences

### Positive
- Benefit 1
- Benefit 2

### Negative
- Challenge 1
- Challenge 2

## Alternatives Considered

- Alternative 1
- Alternative 2
```

---

## Quality Checklist

Before considering a new stack complete, verify:

### Documentation
- [ ] Technology added to all quick reference tables
- [ ] Detailed documentation created in appropriate folder
- [ ] Decision tree updated
- [ ] Examples added throughout documentation
- [ ] README files updated

### Instruction File
- [ ] Follows template format (130-200 lines)
- [ ] Contains technology stack details
- [ ] Includes project structure
- [ ] Documents core patterns
- [ ] Lists testing standards
- [ ] Covers anti-patterns
- [ ] Lists dependencies
- [ ] Includes YAML frontmatter with `applyTo` patterns

### Scaffold Prompt
- [ ] Follows template format (700-900 lines)
- [ ] Service specification form included
- [ ] Architecture diagram present
- [ ] Complete directory structure
- [ ] 18-20 implementation steps
- [ ] Full code examples for all core files
- [ ] Production-ready Dockerfile
- [ ] docker-compose.yml integration
- [ ] DevContainer configuration
- [ ] README template
- [ ] Completion checklist

### Service Configuration
- [ ] Naming convention matches `{type}-{tech}-{purpose}` pattern
- [ ] Directory structure is consistent
- [ ] DevContainer configuration included

### Docker & Deployment
- [ ] Multi-stage Dockerfile (build + production)
- [ ] Non-root user in Docker image
- [ ] Health check endpoint
- [ ] Logging configuration
- [ ] Metrics/monitoring setup
- [ ] Graceful shutdown handling

### Testing
- [ ] Unit test structure
- [ ] Integration test structure
- [ ] E2E test examples (where applicable)
- [ ] Test commands documented
- [ ] Coverage configuration

### Environment & Security
- [ ] `.env.example` file
- [ ] No hardcoded secrets
- [ ] Environment variable documentation
- [ ] Security best practices followed

### Sample Service
- [ ] Test service successfully created
- [ ] Service builds without errors
- [ ] Service runs successfully
- [ ] Tests pass
- [ ] DevContainer works
- [ ] Docker Compose integration works
- [ ] Health check responds
- [ ] Logs are structured
- [ ] Metrics are exposed (if applicable)

### Cross-References
- [ ] Links work in all documentation
- [ ] Navigation flows correctly
- [ ] Examples are consistent
- [ ] No broken references

---

## Example: Adding Rust Actix-Web

Here's a complete example of adding a new stack:

### 1. Update Documentation

**docs/tech-stacks/README.md:**
```markdown
| **APIs** | ... | FastAPI, NestJS, Fiber, **Actix** | [→ apis/](./apis/) |
```

**docs/tech-stacks/NAMING-CONVENTIONS.md:**
```markdown
### APIs
- `fastapi` - Python FastAPI
- `nestjs` - Node.js NestJS
- `fiber` - Go Fiber
- `actix` - Rust Actix-Web  ← NEW
```

**docs/tech-stacks/DECISION-TREE.md:**
```
├─ REST/GraphQL API?
│  └─ YES → Language preference?
│     ├─ Python → api-fastapi-*
│     ├─ Node.js/TypeScript → api-nestjs-*
│     ├─ Go → api-fiber-*
│     └─ Rust → api-actix-*  ← NEW
│                 (Memory safety + performance)
│                 (Perfect for: System-level APIs)
```

### 2. Create Technology Documentation

**docs/tech-stacks/apis/actix.md:**
```markdown
# Actix-Web - API Services

## Overview

Actix-Web is a powerful, pragmatic, and extremely fast web framework for Rust.

## Naming Convention

`api-actix-{purpose}`

**Examples:**
- `api-actix-auth`
- `api-actix-gateway`
- `api-actix-metrics`

## Technology Stack

- Rust 1.70+
- Actix-Web 4.x
- Tokio (async runtime)
- Diesel (ORM)
- Serde (serialization)

## Ideal Use Cases

1. **System-Level APIs**
   - Low-level system integration
   - Performance-critical services
   - Memory-safe system services

2. **High-Performance APIs**
   - Sub-millisecond latency requirements
   - High concurrent load
   - Resource-constrained environments

[... continue with full documentation]
```

### 3. Create Instruction File

**templates/.github/instructions/service-api-actix.instructions.md:**
```markdown
---
applyTo:
  - "services/api-actix-*/**"
  - ".devcontainer/api-actix-*-container/**"
description: "Actix-Web API development patterns and best practices"
---

# Actix-Web API Instructions

## Technology Stack

- Rust 1.70+
- Actix-Web 4.x
- Tokio (async runtime)
- Diesel 2.x (ORM)
- Serde (JSON)

## Project Structure

\`\`\`
services/api-actix-{purpose}/
├── src/
│   ├── main.rs
│   ├── handlers/
│   ├── models/
│   ├── db/
│   └── middleware/
├── tests/
├── Cargo.toml
├── Dockerfile
└── README.md
\`\`\`

[... continue with patterns, testing, etc.]
```

### 4. Create Scaffold Prompt

**templates/.github/prompts/scaffold-api-actix-service.md:**
```markdown
# Scaffold Actix-Web API Service

## Service Specification

**Service Name:** `api-actix-{purpose}`
**Purpose:** [Brief description]

[... complete scaffold with 700-900 lines]
```

### 5. Test Implementation

```bash
# Create test service
# (Using GitHub Copilot with the scaffold prompt)

# Build
cd services/api-actix-test
cargo build

# Run tests
cargo test

# Build Docker
docker build -t api-actix-test .

# Run
docker-compose up api-actix-test

# Test health endpoint
curl http://localhost:8080/health
```

### 6. Update Related Docs

- Create `docs/tech-stacks/apis/actix.md` with complete documentation
- Update `docs/tech-stacks/apis/README.md` with Actix entry
- Add to `docs/getting-started/` examples
- Create ADR: `docs/adr/005-actix-web-stack.md`

---

## Common Pitfalls

### ❌ Incomplete Documentation

Don't just add the technology - update ALL documentation:
- Quick reference tables
- Decision trees
- Examples throughout

### ❌ Missing Tests

Always include test structure and examples in:
- Instruction file
- Scaffold prompt
- Sample service

### ❌ Non-Production Docker

Ensure Dockerfile is:
- Multi-stage
- Secure (non-root user)
- Optimized (minimal image size)
- Includes health check

### ❌ Inconsistent Naming

Follow the exact pattern:
- `{type}-{tech}-{purpose}`
- kebab-case
- No abbreviations in type/tech

---

## Getting Help

- Review existing stacks as examples
- Check instruction files in `templates/.github/instructions/`
- Check scaffold prompts in `templates/.github/prompts/`
- Refer to this guide for the complete process

---

## See Also

- **[Naming Conventions](./NAMING-CONVENTIONS.md)** - Service naming rules
- **[Decision Tree](./DECISION-TREE.md)** - Technology selection
- **[Anti-Patterns](./ANTI-PATTERNS.md)** - What to avoid
- **[Tech Stacks Overview](./README.md)** - All available stacks

---

**Last Updated:** 2025-10-09  
**Template Version:** 1.0.0
