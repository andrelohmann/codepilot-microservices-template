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

## 🏗️ The Three-File System Architecture

The template uses a **three-layer documentation system** where each file has a distinct purpose and audience:

### 1. Tech-Stack Documentation (Source of Truth)

**Location:** `docs/tech-stacks/{service-type}/{tech}.md`  
**Purpose:** Complete implementation reference  
**Audience:** Developers and AI assistants  
**Size:** 200-500 lines (comprehensive)

**Contains:**
- Complete technology overview and ecosystem
- Detailed architecture patterns
- Full code examples and implementations
- Testing strategies with complete examples
- Deployment configurations
- Monitoring and observability setup
- Performance optimization guidelines
- Security best practices
- Troubleshooting guides
- Migration paths and upgrade strategies

**Role:** The single source of truth for all technical details. When in doubt, refer to this document.

### 2. Instruction Files (Copilot Guidance)

**Location:** `templates/.github/instructions/service-{type}-{tech}.instructions.md`  
**Purpose:** GitHub Copilot pattern recognition  
**Audience:** GitHub Copilot (AI assistant)  
**Size:** 130-200 lines (concise, pattern-focused)

**Contains:**
- YAML frontmatter with `applyTo` patterns (maps to directory patterns)
- Core technology stack versions
- Essential project structure
- Key implementation patterns (not exhaustive)
- Testing standards (frameworks and approach)
- Anti-patterns (what NOT to do)
- Critical dependencies list
- **References to tech-stack docs** for complete details

**Role:** Provides just-in-time context to Copilot when working in service directories. Auto-applied based on `applyTo` patterns.

**Key Principle:** Should be **concise** and **reference** the tech-stack docs rather than duplicate content.

### 3. Scaffold Prompts (Automation Templates)

**Location:** `templates/.github/prompts/scaffold-{type}-{tech}-service.prompt.md`  
**Purpose:** Automated service scaffolding  
**Audience:** VS Code Prompt UI (for user-initiated scaffolding)  
**Size:** 60-160 lines (streamlined, input-driven)

**Contains:**
- YAML frontmatter with VS Code prompt metadata
  - `description`: Brief prompt description
  - `mode: agent`: Enables agentic workflow
  - `tools: [file, workspace, terminal]`: Available capabilities
- **Input variables** using `${input:variableName:defaultValue}` syntax
- High-level scaffolding steps (not exhaustive)
- **References to both instruction files AND tech-stack docs**
- Core file templates (simplified, not full implementations)
- Docker and DevContainer basics
- Completion checklist

**Role:** Provides a guided, interactive scaffolding experience. Leverages VS Code's prompt UI for user inputs.

**Key Principle:** Should be **streamlined** and **reference** other files rather than contain full implementations.

---

## 📐 Alignment Principles

### Relationship Between Files

```
┌─────────────────────────────────────────────────────────────┐
│  Tech-Stack Docs (Source of Truth)                          │
│  docs/tech-stacks/{type}/{tech}.md                          │
│  • Complete implementation details                          │
│  • Full code examples                                       │
│  • Comprehensive guides                                     │
└────────────────┬────────────────────────────────────────────┘
                 │
                 │ References
                 │
      ┌──────────┴──────────┐
      │                     │
      ▼                     ▼
┌─────────────────┐   ┌─────────────────────────────┐
│ Instructions    │   │ Scaffold Prompts            │
│ (Copilot)       │   │ (VS Code)                   │
│ • Concise       │   │ • Interactive               │
│ • Patterns      │   │ • Input-driven              │
│ • References    │   │ • References both           │
└─────────────────┘   └─────────────────────────────┘
```

### Content Distribution Guidelines

| Content Type | Tech-Stack Docs | Instructions | Prompts |
|--------------|----------------|--------------|---------|
| **Complete implementations** | ✅ Full | ❌ None | ❌ None |
| **Code examples** | ✅ Many | ✅ Key patterns only | ❌ None |
| **Architecture diagrams** | ✅ Detailed | ✅ Simple | ✅ Simple |
| **Testing strategies** | ✅ Complete | ✅ Standards only | ✅ Checklist only |
| **Configuration details** | ✅ All options | ✅ Critical only | ✅ Minimal |
| **References to other files** | ❌ None | ✅ Yes | ✅ Yes |
| **User input prompts** | ❌ No | ❌ No | ✅ Yes |

### Anti-Duplication Strategy

**❌ Don't:**
- Copy full implementations across files
- Duplicate code examples
- Repeat detailed explanations
- Create redundant content

**✅ Do:**
- Reference the tech-stack docs for details
- Extract only essential patterns for instructions
- Create concise prompts with input variables
- Link between files explicitly

**Example Reference Pattern:**

```markdown
<!-- In instruction file -->
## See Also

- [FastAPI Complete Documentation](../../docs/tech-stacks/apis/fastapi.md)
- [API Overview](../../docs/tech-stacks/apis/README.md)

<!-- In prompt file -->
## Reference Documentation

**Complete Implementation Details:**
- Tech-Stack Docs: `docs/tech-stacks/apis/fastapi.md`
- Instruction File: `templates/.github/instructions/service-api-fastapi.instructions.md`
```

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

Create `templates/.github/prompts/scaffold-{type}-{tech}-service.prompt.md`

**VS Code Prompt Format (YAML Frontmatter + Markdown):**

The prompt files use VS Code's native prompt format with three key components:

1. **YAML Frontmatter** - Metadata for VS Code
2. **Input Variables** - Interactive user prompts
3. **Scaffolding Instructions** - Concise implementation steps

**Template Structure:**

```markdown
---
description: Scaffold a new {Technology} {Service Type} service
mode: agent
tools:
  - file
  - workspace
  - terminal
---

# Scaffold {Technology} {Service Type} Service

## Service Information

**Service Name:** ${input:serviceName:api-fastapi-example}
**Service Purpose:** ${input:servicePurpose:Example API service}

**Additional Features:**
${input:features:
- [ ] Authentication
- [ ] Database integration
- [ ] Caching
- [ ] Rate limiting
}

---

## Reference Documentation

**Complete Implementation Details:**
- Tech-Stack Docs: `docs/tech-stacks/{service-type}/{tech}.md`
- Instruction File: `templates/.github/instructions/service-{type}-{tech}.instructions.md`

Refer to these files for complete implementation patterns, testing strategies, and best practices.

---

## Architecture Overview

\`\`\`
[Simple ASCII Architecture Diagram]
\`\`\`

---

## Implementation Steps

### Step 1: Create Project Structure

Create the service directory with the following structure:

\`\`\`
services/${input:serviceName}/
├── src/
├── tests/
├── Dockerfile
├── .dockerignore
├── {config-file}
├── .env.example
└── README.md
\`\`\`

### Step 2: Initialize Configuration

Create {config-file} with essential configuration.

### Step 3: Implement Core Application

Create main application file at src/main.{ext}.

### Step 4: Add Docker Configuration

Create production-ready multi-stage Dockerfile.

### Step 5: Create DevContainer

Set up .devcontainer/devcontainer.json for development.

### Step 6: Add Documentation

Create comprehensive README.md.

### Step 7: Verify Service

Run the completion checklist below.

---

## Completion Checklist

After scaffolding, verify:

- [ ] Service structure matches template
- [ ] Service runs locally
- [ ] Docker build successful
- [ ] DevContainer works
- [ ] Tests pass
- [ ] Health check responds
- [ ] Environment variables documented
- [ ] README complete
- [ ] Logging configured
- [ ] Production-ready

---

## Next Steps

1. Review generated code
2. Implement business logic
3. Add integration tests
4. Configure CI/CD
5. Deploy to development environment

For complete implementation patterns and best practices, refer to:
- `docs/tech-stacks/{service-type}/{tech}.md`
```

**Key Components Explained:**

**1. YAML Frontmatter:**
```yaml
---
description: Brief description shown in VS Code prompt picker
mode: agent              # Enables agentic workflow with tool access
tools:                   # Available capabilities
  - file                 # File operations
  - workspace            # Workspace operations  
  - terminal             # Terminal commands
---
```

**2. Input Variables:**
```markdown
${input:variableName:defaultValue}
```

Used for:
- Service name
- Service purpose
- Optional features
- Configuration values

**3. Concise Instructions:**
- High-level steps only (not exhaustive)
- Reference tech-stack docs for complete details
- Focus on scaffolding workflow
- Include completion checklist

**Size Guidelines:**
- **Old format:** 700-900 lines (exhaustive)
- **New format:** 60-160 lines (concise + references)
- **Reduction:** ~90% shorter

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

### Instruction File (130-200 lines)
- [ ] Follows template format
- [ ] YAML frontmatter with `applyTo` patterns
- [ ] Contains technology stack details
- [ ] Includes project structure
- [ ] Documents core patterns (not exhaustive)
- [ ] Lists testing standards
- [ ] Covers anti-patterns
- [ ] Lists critical dependencies
- [ ] **References tech-stack docs** for complete details
- [ ] No duplicate content from tech-stack docs

### Scaffold Prompt (60-160 lines, .prompt.md format)
- [ ] Follows VS Code prompt format
- [ ] YAML frontmatter with `description`, `mode: agent`, `tools`
- [ ] Input variables using `${input:name:default}` syntax
- [ ] Architecture diagram present
- [ ] High-level implementation steps (concise)
- [ ] Completion checklist included
- [ ] **References both instruction file AND tech-stack docs**
- [ ] No duplicate code examples
- [ ] No full implementations (only references)

### Alignment Between Files
- [ ] **No content duplication** across three files
- [ ] Tech-stack doc is the single source of truth
- [ ] Instruction file references tech-stack doc
- [ ] Prompt file references both instruction and tech-stack docs
- [ ] Each file has distinct purpose and audience
- [ ] Content distribution follows guidelines

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

Here's a complete example of adding a new stack with the three-file system:

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

### 2. Create Technology Documentation (Source of Truth)

**docs/tech-stacks/apis/actix.md (~400 lines):**
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

## Complete Implementation

### Main Application (src/main.rs)

\`\`\`rust
use actix_web::{web, App, HttpServer, HttpResponse};
use actix_web::middleware::Logger;

#[actix_web::main]
async fn main() -> std::io::Result<()> {
    env_logger::init();
    
    HttpServer::new(|| {
        App::new()
            .wrap(Logger::default())
            .route("/health", web::get().to(health_check))
            .route("/api/v1/resource", web::get().to(get_resources))
    })
    .bind("0.0.0.0:8080")?
    .run()
    .await
}

async fn health_check() -> HttpResponse {
    HttpResponse::Ok().json(serde_json::json!({
        "status": "healthy",
        "service": "api-actix-example"
    }))
}

// ... complete implementation
\`\`\`

### Configuration (Cargo.toml)

\`\`\`toml
[package]
name = "api-actix-service"
version = "0.1.0"
edition = "2021"

[dependencies]
actix-web = "4.x"
tokio = { version = "1", features = ["full"] }
serde = { version = "1", features = ["derive"] }
serde_json = "1"
env_logger = "0.10"
diesel = { version = "2", features = ["postgres"] }

# ... complete dependencies
\`\`\`

## Testing Strategies

### Unit Tests

\`\`\`rust
#[cfg(test)]
mod tests {
    use super::*;
    use actix_web::{test, App};

    #[actix_web::test]
    async fn test_health_check() {
        let app = test::init_service(
            App::new().route("/health", web::get().to(health_check))
        ).await;
        
        let req = test::TestRequest::get()
            .uri("/health")
            .to_request();
            
        let resp = test::call_service(&app, req).await;
        assert!(resp.status().is_success());
    }
}
\`\`\`

## Deployment

### Production Dockerfile

\`\`\`dockerfile
# Build stage
FROM rust:1.70-slim as builder
WORKDIR /app
COPY Cargo.toml Cargo.lock ./
COPY src ./src
RUN cargo build --release

# Production stage
FROM debian:bookworm-slim
RUN apt-get update && apt-get install -y libpq5 && rm -rf /var/lib/apt/lists/*
COPY --from=builder /app/target/release/api-actix-service /usr/local/bin/
USER 1000
EXPOSE 8080
CMD ["api-actix-service"]
\`\`\`

## Performance Optimization

[... detailed performance tuning strategies]

## Security Best Practices

[... comprehensive security guidelines]

## Monitoring & Observability

[... complete monitoring setup]

## Troubleshooting

[... common issues and solutions]

## See Also

- [API Services Overview](./README.md)
- [Naming Conventions](../NAMING-CONVENTIONS.md)
- [Decision Tree](../DECISION-TREE.md)
```

### 3. Create Instruction File (Copilot Guidance)

**templates/.github/instructions/service-api-actix.instructions.md (~150 lines):**
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
│   └── db/
├── tests/
├── Cargo.toml
├── Dockerfile
└── README.md
\`\`\`

## Core Patterns

### Pattern 1: Handler Functions

\`\`\`rust
async fn handler_name(data: web::Data<AppState>) -> HttpResponse {
    // Handler logic
    HttpResponse::Ok().json(response)
}
\`\`\`

### Pattern 2: Middleware

\`\`\`rust
use actix_web::middleware::Logger;

App::new()
    .wrap(Logger::default())
    .route("/api/v1/resource", web::get().to(handler))
\`\`\`

## Testing Standards

### Unit Tests
- Use `actix_web::test` utilities
- Mock external dependencies
- Test handlers independently

### Integration Tests
- Test complete request/response cycle
- Use test database
- Verify error handling

## Anti-Patterns

### ❌ Blocking Operations in Async Handlers

Don't use blocking I/O in async functions.

### ✅ Use Tokio Spawn Blocking

\`\`\`rust
web::block(|| {
    // Blocking operation
}).await
\`\`\`

## Dependencies

### Production
- actix-web 4.x
- tokio (async runtime)
- serde (serialization)

### Development
- actix-web-test
- cargo-watch

## See Also

- [Actix-Web Complete Documentation](../../docs/tech-stacks/apis/actix.md)
- [API Overview](../../docs/tech-stacks/apis/README.md)
```

### 4. Create Scaffold Prompt (VS Code Format)

**templates/.github/prompts/scaffold-api-actix-service.prompt.md (~120 lines):**
```markdown
---
description: Scaffold a new Actix-Web API service
mode: agent
tools:
  - file
  - workspace
  - terminal
---

# Scaffold Actix-Web API Service

## Service Information

**Service Name:** ${input:serviceName:api-actix-example}
**Service Purpose:** ${input:servicePurpose:Example API service}

**Features:**
${input:features:
- [ ] Authentication
- [ ] Database integration
- [ ] Caching
- [ ] Rate limiting
}

---

## Reference Documentation

**Complete Implementation Details:**
- Tech-Stack Docs: `docs/tech-stacks/apis/actix.md`
- Instruction File: `templates/.github/instructions/service-api-actix.instructions.md`

Refer to these files for complete implementation patterns, testing strategies, and best practices.

---

## Architecture Overview

\`\`\`
┌─────────────────┐
│   API Gateway   │
└────────┬────────┘
         │
    ┌────▼─────┐
    │  Actix   │
    │  Router  │
    └────┬─────┘
         │
    ┌────▼─────┐
    │ Handlers │
    └────┬─────┘
         │
    ┌────▼─────┐
    │ Database │
    └──────────┘
\`\`\`

---

## Implementation Steps

### Step 1: Create Project Structure

Create the service directory:

\`\`\`
services/${input:serviceName}/
├── src/
│   ├── main.rs
│   └── handlers/
├── tests/
├── Cargo.toml
├── Dockerfile
└── README.md
\`\`\`

### Step 2: Initialize Cargo Configuration

Create Cargo.toml with required dependencies.

### Step 3: Implement Main Application

Create src/main.rs with HTTP server setup.

### Step 4: Add Health Check Endpoint

Implement basic health check at /health.

### Step 5: Create Dockerfile

Add production-ready multi-stage Dockerfile.

### Step 6: Add DevContainer Configuration

Set up .devcontainer/devcontainer.json.

### Step 7: Create Documentation

Add comprehensive README.md.

---

## Completion Checklist

After scaffolding, verify:

- [ ] Service structure created
- [ ] Cargo.toml configured
- [ ] Main application implemented
- [ ] Health check responds
- [ ] Docker build successful
- [ ] Tests pass
- [ ] DevContainer works
- [ ] README complete

---

## Next Steps

1. Implement business logic in handlers
2. Add database integration
3. Write integration tests
4. Configure CI/CD pipeline

For complete patterns, refer to `docs/tech-stacks/apis/actix.md`.
```

### 5. File Size Comparison

| File | Old Format | New Format | Reduction |
|------|-----------|-----------|-----------|
| **Tech-Stack Doc** | 400 lines | 400 lines | 0% (source of truth) |
| **Instruction File** | 200 lines | 150 lines | 25% (concise patterns) |
| **Scaffold Prompt** | 850 lines | 120 lines | 86% (references only) |
| **Total** | 1,450 lines | 670 lines | **54% reduction** |

### 6. Content Distribution

**Tech-Stack Doc (400 lines):**
- Complete implementation examples ✅
- Full configuration files ✅
- Comprehensive testing strategies ✅
- Deployment guides ✅
- Performance optimization ✅
- Security best practices ✅

**Instruction File (150 lines):**
- Core patterns only ✅
- Essential anti-patterns ✅
- Testing standards (not examples) ✅
- References tech-stack doc ✅

**Scaffold Prompt (120 lines):**
- Input variables for customization ✅
- High-level steps ✅
- References both files ✅
- No duplicate code ✅

---

## Common Pitfalls

### ❌ Content Duplication

**Problem:** Copying full implementations across all three files.

**Solution:**
- Put complete details ONLY in tech-stack docs
- Reference tech-stack docs from instruction files
- Reference both files from scaffold prompts
- Extract only essential patterns for instructions

**Example:**
```markdown
<!-- ❌ Wrong: Duplicating in instruction file -->
## Database Connection

Complete implementation with 50 lines of code...

<!-- ✅ Right: Reference + essential pattern -->
## Database Connection

See complete implementation in `docs/tech-stacks/apis/fastapi.md#database-connection`

Essential pattern:
\`\`\`python
db = Database(settings.database_url)
\`\`\`
```

### ❌ Missing VS Code Prompt Format

**Problem:** Using old markdown format instead of VS Code prompt format.

**Solution:**
- Always include YAML frontmatter
- Use `mode: agent` for agentic workflows
- Define `tools: [file, workspace, terminal]`
- Use `.prompt.md` file extension

**Example:**
```markdown
<!-- ✅ Right: VS Code format -->
---
description: Scaffold a new FastAPI service
mode: agent
tools:
  - file
  - workspace
  - terminal
---

# Scaffold FastAPI Service

**Service Name:** ${input:serviceName:api-fastapi-example}
```

### ❌ Missing Input Variables

**Problem:** Hard-coding values instead of using input variables.

**Solution:**
- Use `${input:variableName:defaultValue}` syntax
- Prompt for service name, purpose, features
- Make prompts interactive and reusable

**Example:**
```markdown
<!-- ❌ Wrong: Hard-coded -->
**Service Name:** api-fastapi-example

<!-- ✅ Right: Input variable -->
**Service Name:** ${input:serviceName:api-fastapi-example}
```

### ❌ Incomplete Documentation

**Problem:** Adding technology but not updating all documentation.

**Solution:** Update ALL documentation:
- Quick reference tables
- Decision trees
- Naming conventions
- Examples throughout

### ❌ Missing Tests

**Problem:** No test structure or examples.

**Solution:** Always include test structure and examples in:
- Tech-stack docs (complete examples)
- Instruction file (testing standards)
- Scaffold prompt (test checklist)
- Sample service (working tests)

### ❌ Non-Production Docker

**Problem:** Development-only Dockerfile.

**Solution:** Ensure Dockerfile is:
- Multi-stage (build + production)
- Secure (non-root user)
- Optimized (minimal image size)
- Includes health check
- Production-ready

### ❌ Inconsistent Naming

**Problem:** Not following naming conventions.

**Solution:** Follow the exact pattern:
- `{type}-{tech}-{purpose}`
- kebab-case
- No abbreviations in type/tech
- Use official technology names

### ❌ Oversized Prompt Files

**Problem:** 700-900 line prompt files with full implementations.

**Solution:**
- Keep prompts 60-160 lines
- Use input variables for customization
- Reference tech-stack docs for details
- Focus on scaffolding workflow, not complete implementation

---

## Getting Help

- Review existing stacks as examples
- Check instruction files in `templates/.github/instructions/`
- Check scaffold prompts in `templates/.github/prompts/`
- Refer to this guide for the complete process
- Follow the three-file system architecture
- Maintain alignment between files

---

## Key Takeaways

### The Three-File System

1. **Tech-Stack Docs** = Complete source of truth (200-500 lines)
2. **Instruction Files** = Concise Copilot guidance (130-200 lines)
3. **Scaffold Prompts** = Interactive VS Code prompts (60-160 lines)

### Alignment Principles

- **No duplication** - Each file has distinct purpose
- **Reference, don't repeat** - Link between files
- **Complete once, reference many** - Single source of truth

### VS Code Prompt Format

```yaml
---
description: Brief description
mode: agent
tools: [file, workspace, terminal]
---
```

### Input Variables

```markdown
${input:variableName:defaultValue}
```

### File Naming

- Instruction files: `service-{type}-{tech}.instructions.md`
- Scaffold prompts: `scaffold-{type}-{tech}-service.prompt.md`
- Tech-stack docs: `{tech}.md`

---

## See Also

- **[Naming Conventions](./NAMING-CONVENTIONS.md)** - Service naming rules
- **[Decision Tree](./DECISION-TREE.md)** - Technology selection
- **[Anti-Patterns](./ANTI-PATTERNS.md)** - What to avoid
- **[Tech Stacks Overview](./README.md)** - All available stacks
- **[Alignment TODOs](./ALIGNMENT-TODOS.md)** - Alignment project progress

---

**Last Updated:** 2025-01-XX  
**Template Version:** 2.0.0 (Three-File System with VS Code Prompts)
