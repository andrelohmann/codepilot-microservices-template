# MICROSERVICES-COMPONENT-CATALOG.md

Dieser Katalog beschreibt Standard-Bausteine für Microservices-Architekturen nach 12-Factor-Prinzipien.  
Zu jedem Baustein: Zweck, typische Technologien, ENV-Beispiele, „When to use / When not to use“.

---

## 1. Edge / Client Access

### API-Gateway / Ingress
**Zweck:** Einheitlicher Entry Point, Auth, Routing, Rate Limits, Canary Deployments.  
**Beispiele:** Kong, APISIX, Envoy, NGINX, AWS API Gateway.  
**ENV:**
```env
GATEWAY_URL=https://api.example.com
```

**When to use:** Viele Services, externe APIs.
**When not to use:** Kleines System mit wenigen Services.

### Backend-for-Frontend (BFF)

**Zweck:** API-Adaption für Web/Mobile.
**Beispiele:** Node.js Express BFF, GraphQL BFF.
**When to use:** Unterschiedliche Client-Typen.
**When not to use:** Ein einziger einfacher Client.

### CDN / Asset Delivery

**Zweck:** Latenzreduktion, Caching, statische Assets.
**Beispiele:** CloudFront, Cloudflare, Fastly.
**When to use:** SPA/Mobile mit globalen Nutzern.
**When not to use:** Rein internes Backend.

---

## 2. Core Services

### Domain-Services

**Zweck:** Fachlogik pro Bounded Context.
**Beispiele:** FastAPI, Spring Boot, NestJS.
**ENV:**

```env
SERVICE_PORT=8080
```

**When to use:** Standard für Microservices.
**When not to use:** Prototyp ohne Trennung.

### Aggregator / Composer

**Zweck:** Aggregiert Daten mehrerer Services.
**Beispiele:** BFF, GraphQL Gateway.
**When to use:** UI-nahe Komposition.
**When not to use:** Enge Kopplung vermeiden.

### Anti-Corruption Layer

**Zweck:** Entkopplung zu Legacy-Systemen.
**Beispiele:** Mapping-Service, Adapter.
**When to use:** Integration mit Fremdsystem.
**When not to use:** Nur grüne Wiese.

### Notification-Service

**Zweck:** E-Mail, SMS, Push, Webhooks.
**Beispiele:** Postmark, Twilio, eigener Service.
**ENV:**

```env
SMTP_HOST=smtp.example.com
```

---

## 3. Data & Async

### Datenbanken

**Zweck:** Persistenz pro Service.
**Beispiele:** PostgreSQL, MySQL, MongoDB, Cassandra.
**ENV:**

```env
DATABASE_URL=postgres://user:pass@db:5432/app
```

### Object Storage

**Zweck:** Binäre Assets, Dateien.
**Beispiele:** S3, MinIO.
**ENV:**

```env
OBJECT_STORAGE_URL=s3://bucket
```

### Cache / KV-Store

**Zweck:** Schneller Zugriff, Sessions, Rate-Limiting.
**Beispiele:** Redis, Memcached.
**ENV:**

```env
CACHE_URL=redis://cache:6379
```

### Queues / Streams

**Zweck:** Entkopplung, Events, Work Distribution.
**Beispiele:** Kafka, RabbitMQ, SQS, NATS.
**ENV:**

```env
QUEUE_URL=amqp://user:pass@queue:5672
```

### Event Store / Audit-Log

**Zweck:** Unveränderliche Fakten, Replays.
**Beispiele:** EventStoreDB, Kafka-Log.

### Scheduler / Cron

**Zweck:** Zeitgesteuerte Tasks.
**Beispiele:** Kubernetes CronJob, Airflow, Argo.
**ENV:**

```env
CRON_EXPRESSION=0 0 * * *
```

### Workflow / Orchestrator

**Zweck:** Sagas, State-Machines, lang laufende Prozesse.
**Beispiele:** Temporal, Camunda, Step Functions.

---

## 4. Cross-Cutting Concerns

### Identity & Access Management

**Zweck:** AuthN/AuthZ für User & Services.
**Beispiele:** Keycloak, Auth0, Cognito.
**ENV:**

```env
OIDC_ISSUER=https://id.example.com
```

### Secret Management

**Zweck:** Sichere Verwaltung von Keys & Tokens.
**Beispiele:** Vault, AWS Secrets Manager, SOPS.
**ENV:**

```env
SECRET_PATH=secret/data/app
```

### Config Management

**Zweck:** Zentrale Konfiguration, Feature Flags.
**Beispiele:** Consul, Spring Cloud Config, Unleash.
**ENV:**

```env
FEATURE_FLAG_NEWUI=true
```

### Observability

**Zweck:** Logs, Metrics, Tracing.
**Beispiele:** Prometheus, Grafana, Loki, Jaeger, OpenTelemetry.
**ENV:**

```env
OTEL_EXPORTER_OTLP_ENDPOINT=http://collector:4317
```

### Resilience Patterns

**Zweck:** Stabilität, Fehlertoleranz.
**Beispiele:** Circuit Breaker, Bulkhead, Retries.
**When to use:** Verteilte Systeme.
**When not to use:** Single-Monolith.

---

## 5. Platform & Ops

### Container Orchestrierung

**Zweck:** Deployment, Skalierung, Self-Healing.
**Beispiele:** Kubernetes, Nomad.
**When to use:** >3 Services, Cluster-Betrieb.
**When not to use:** Einfache PoCs.

### Service Mesh

**Zweck:** mTLS, Routing, Observability.
**Beispiele:** Istio, Linkerd, Cilium.
**When to use:** Hohe Service-Zahl, Security/Compliance.
**When not to use:** Kleine Deployments.

### CI/CD & Artefakte

**Zweck:** Automatisiertes Build→Test→Deploy.
**Beispiele:** GitLab CI, GitHub Actions, ArgoCD, Flux.
**ENV:**

```env
CI_ENVIRONMENT_NAME=production
```

### Developer Portal / API-Katalog

**Zweck:** Discoverability, Docs, Golden Paths.
**Beispiele:** Backstage, OpenAPI Registry.

---

## 6. Standard-Architekturmuster

* **Hexagonal / Clean Architecture:** Pro Service Domain vs. Adapter sauber trennen.
* **DDD (Domain-Driven Design):** Bounded Contexts, Aggregate Roots, Domain Events.
* **Event-Driven Architecture (EDA):** Entkopplung via Pub/Sub, Outbox/Inbox-Pattern.
* **CQRS & Event Sourcing:** Schreib-/Lese-Modelle trennen, Events als Quelle.
* **Sagas:** Verteilte Transaktionen über Events oder Orchestratoren.
* **Strangler Fig Pattern:** Legacy stückweise ersetzen.
* **GitOps:** Deklarative Deployments, Auditierbarkeit.
* **Micro-Frontends + BFF:** Skalierung auch auf UI-Ebene.

---
