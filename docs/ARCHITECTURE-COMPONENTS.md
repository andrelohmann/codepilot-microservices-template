# ARCHITECTURE-COMPONENTS.md

Dieses Dokument beschreibt zentrale Software-Bausteine einer Microservices-Architektur nach 12-Factor-Prinzipien. Jeder Abschnitt enthält Zweck, typische Technologien sowie Hinweise zu „When to use / When not to use“.

---

## 1. Persistenz-Layer

**Zweck:** Zustandsablage pro Service, Speicherung von Assets und Dateien.  
**Beispiele:** PostgreSQL, MySQL, MongoDB, DynamoDB, Cassandra, Neo4j, Elasticsearch, InfluxDB, MinIO/S3.  
**When to use:** Service benötigt dauerhaften Zustand, strukturierte oder unstrukturierte Daten.  
**When not to use:** Reine Compute- oder Cache-Services ohne State.

**ENV-Beispiel:**
```env
DATABASE_URL=postgres://user:pass@db:5432/service
OBJECT_STORAGE_URL=s3://bucket-name
```

---

## 2. Queues, Pipelines, Caches

**Zweck:** Entkopplung, Lastglättung, Asynchronität, schnelle Zugriffe.
**Beispiele:** RabbitMQ, SQS, Kafka, NATS, Flink, Redis, Memcached.
**When to use:** Verarbeitung nicht-blockierend, Event-getrieben, Skalierung von Workloads.
**When not to use:** Kritisch synchrone Operationen, einfache Request/Response.

**ENV-Beispiel:**

```env
QUEUE_URL=amqp://user:pass@queue:5672
CACHE_URL=redis://cache:6379
```

---

## 3. Frontend (SPA, Mobile, Micro-Frontend)

**Zweck:** Benutzeroberfläche, Interaktion mit APIs.
**Beispiele:** React, Vue, Angular, Next.js, Flutter, React Native.
**When to use:** Nutzer-facing UI, klare Trennung von Backend.
**When not to use:** Reine Backend-Systeme.

---

## 4. REST / gRPC / GraphQL APIs

**Zweck:** Synchrone Kommunikation, Verträge zwischen Services.
**Beispiele:** FastAPI, Spring Boot, Express.js, gRPC, Apollo GraphQL.
**When to use:** Niedrige Latenz, Request/Response-Szenarien.
**When not to use:** Entkopplung über Events ausreichend.

---

## 5. ORM / Data Access Layer

**Zweck:** Abstraktion von Datenbanken, Migrationssteuerung.
**Beispiele:** SQLAlchemy, Prisma, TypeORM, Django ORM.
**When to use:** Datenbankzugriff stark, schema-getrieben.
**When not to use:** High-Performance Szenarien mit direktem SQL.

---

## 6. Worker & Background Jobs

**Zweck:** Asynchrone Verarbeitung, Batch, Langläufer.
**Beispiele:** Celery, Sidekiq, Resque, Kubernetes Jobs, AWS Lambda.
**When to use:** Nicht-blockierende oder planbare Tasks.
**When not to use:** Harte Latenzanforderungen in synchronen APIs.

---

## 7. API Gateway & Edge

**Zweck:** Einheitlicher Entry Point, Auth, Routing, Rate Limiting.
**Beispiele:** Kong, APISIX, Envoy, NGINX, AWS API Gateway.
**When to use:** Viele Services, einheitliche Policies.
**When not to use:** Kleine Systeme mit 1–2 Services.

---

## 8. Identity & Access Management

**Zweck:** Authentifizierung & Autorisierung für User & Services.
**Beispiele:** Keycloak, Auth0, Ory, Cognito.
**When to use:** Multi-Tenant, sichere APIs, Benutzerverwaltung.
**When not to use:** Interne Prototypen ohne Auth-Anforderungen.

---

## 9. Service Discovery & Routing

**Zweck:** Dynamische Adressierung, resilientes Routing.
**Beispiele:** Consul, etcd, Kubernetes Services.
**When to use:** Dynamische Services, Cluster.
**When not to use:** Statische kleine Deployments.

---

## 10. Event Bus & Event Store

**Zweck:** Lose Kopplung, Event-getriebene Kommunikation.
**Beispiele:** Kafka, Pulsar, NATS JetStream, EventStoreDB.
**When to use:** Skalierung, Auditing, Replays.
**When not to use:** Wenige Services, keine Events.

---

## 11. Workflow & Orchestrierung

**Zweck:** Lang laufende Prozesse, Sagas, State-Machines.
**Beispiele:** Temporal, Camunda, Step Functions, Conductor.
**When to use:** Verteilte Transaktionen, Komplexe Workflows.
**When not to use:** Kleine, einfache Abläufe.

---

## 12. Scheduler / Cron

**Zweck:** Zeitgesteuerte Tasks, Reconciliation-Loops.
**Beispiele:** Kubernetes CronJobs, Airflow, Argo, Dagster.
**When to use:** Periodische Workloads.
**When not to use:** Event-getriebene Systeme.

---

## 13. Feature Flags & Experimentation

**Zweck:** Kontrolle über Feature Releases, A/B-Tests.
**Beispiele:** LaunchDarkly, Unleash, Flipt.
**When to use:** Iterative Releases, Experimente.
**When not to use:** Kleinprojekte ohne Release-Risiko.

---

## 14. Secret Management

**Zweck:** Sichere Verwaltung von Keys, Passwörtern, Tokens.
**Beispiele:** Vault, SOPS, AWS Secrets Manager.
**When to use:** Produktionssysteme, Compliance-Anforderungen.
**When not to use:** Lokale Tests mit Dummy-Secrets.

---

## 15. Logging, Metrics, Tracing

**Zweck:** Beobachtbarkeit, Monitoring, Debugging.
**Beispiele:** Loki, ELK, Prometheus, Grafana, Jaeger, OpenTelemetry.
**When to use:** Alle produktiven Systeme.
**When not to use:** Einfache Proof-of-Concepts.

---

## 16. Resilience Patterns

**Zweck:** Fehlertoleranz, Stabilität.
**Beispiele:** Circuit Breaker, Bulkhead, Retries mit Backoff.
**When to use:** Verteilte Systeme mit unzuverlässigen Netzwerken.
**When not to use:** Monolithische Systeme ohne Netzwerkkopplung.

---

## 17. Container Orchestrierung

**Zweck:** Deployment, Skalierung, Self-Healing.
**Beispiele:** Kubernetes, Nomad, Docker Swarm.
**When to use:** Mehrere Services, Clusterbetrieb.
**When not to use:** Einzelne kleine Deployments.

---

## 18. Service Mesh

**Zweck:** mTLS, Traffic-Shaping, Observability ohne App-Code.
**Beispiele:** Istio, Linkerd, Cilium.
**When to use:** Viele Services mit strengen Security/Traffic-Anforderungen.
**When not to use:** Kleine Systeme.

---

## 19. CI/CD & Artefaktmanagement

**Zweck:** Automatisierte Build-Test-Deploy-Pipelines.
**Beispiele:** GitLab CI, GitHub Actions, Argo CD, Flux, Helm.
**When to use:** Alle produktionsnahen Projekte.
**When not to use:** Prototypen ohne Deployments.

---

## 20. Config-Management

**Zweck:** Dynamische, externe Konfiguration.
**Beispiele:** ENV Variablen, Consul, etcd, Spring Cloud Config.
**When to use:** Mehrstufige Umgebungen, zentrale Verwaltung.
**When not to use:** Minimalprojekte.

---

## 21. Rate-Limiting & Quotas

**Zweck:** Schutz vor Überlast, Abrechnung, Fair Use.
**Beispiele:** Envoy, Kong, Redis-basierte Counter.
**When to use:** Public APIs, Mandantenfähigkeit.
**When not to use:** Kleine interne Systeme.

---

## 22. Developer Experience & Qualität

**Zweck:** Schnelleres Feedback, bessere Wartbarkeit.
**Beispiele:** Contract Tests (Pact), Testcontainers, Devcontainers, Backstage.
**When to use:** Skalierende Teams, viele Services.
**When not to use:** Einzelentwickler-Prototypen.

---

