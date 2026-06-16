# PSTA - Plataforma de Seguridad Transaccional Adaptativa

## Resumen Ejecutivo
- Problema
- Objetivos
- Arquitectura propuesta

## Entregables

### 1. Diseño de Dominio
./02-domain-design

### 2. Arquitectura
./03-architecture

### 3. DevOps
./05-devops

### 4. Infraestructura
./06-infrastructure

### 5. Observabilidad
./07-observability

### 6. Seguridad
./08-security

### 7. Resiliencia
./09-resilience

### 8. Estrategia
./10-strategy

### 9. MVP
./12-mvp




psta-adaptive-security-platform/
│
├── README.md
│
├── 01-executive-summary/
│   ├── business-problem.md
│   ├── objectives.md
│   ├── non-functional-requirements.md
│   └── architecture-principles.md
│
├── 02-domain-design/
│   ├── domain-map.drawio
│   ├── domain-map.pdf
│   ├── bounded-contexts.md
│   ├── ubiquitous-language.md
│   ├── aggregates.md
│   ├── value-objects.md
│   ├── domain-events.md
│   └── event-storming.pdf
│
├── 03-architecture/
│   │
│   ├── logical/
│   │   ├── context-diagram.drawio
│   │   ├── context-diagram.pdf
│   │   ├── container-diagram.drawio
│   │   ├── container-diagram.pdf
│   │   ├── component-risk-domain.drawio
│   │   ├── component-risk-domain.pdf
│   │   ├── component-auth-domain.drawio
│   │   └── component-auth-domain.pdf
│   │
│   ├── physical/
│   │   ├── deployment-diagram.drawio
│   │   ├── network-topology.drawio
│   │   ├── kubernetes-layout.drawio
│   │   └── multi-region-topology.pdf
│   │
│   └── decisions/
│       ├── architecture-style.md
│       ├── event-driven-justification.md
│       ├── cqrs-decision.md
│       ├── kafka-decision.md
│       └── tradeoffs.md
│
├── 04-scenarios/
│   ├── scenario-1-low-risk-transfer.md
│   ├── scenario-2-new-device.md
│   ├── scenario-3-velocity-fraud.md
│   ├── scenario-4-otp-outage.md
│   ├── scenario-5-session-risk-escalation.md
│   │
│   └── sequence-diagrams/
│       ├── scenario-1.drawio
│       ├── scenario-1.pdf
│       ├── scenario-2.pdf
│       ├── scenario-3.pdf
│       ├── scenario-4.pdf
│       └── scenario-5.pdf
│
├── 05-devops/
│   ├── branching-strategy.md
│   ├── ci-cd-pipeline.md
│   ├── deployment-strategy.md
│   ├── testing-strategy.md
│   ├── feature-flags.md
│   └── canary-release-flow.drawio
│
├── 06-infrastructure/
│   ├── high-availability.md
│   ├── disaster-recovery.md
│   ├── autoscaling.md
│   ├── kubernetes-topology.md
│   ├── cost-estimation.md
│   └── hpa-keda-strategy.md
│
├── 07-observability/
│   ├── metrics-catalog.md
│   ├── dashboards.md
│   ├── alerts.md
│   ├── tracing-strategy.md
│   ├── logging-standard.md
│   └── runbooks/
│       ├── otp-provider-down.md
│       ├── kafka-lag.md
│       └── mass-account-blocking.md
│
├── 08-security/
│   ├── zero-trust.md
│   ├── mtls-strategy.md
│   ├── service-authentication.md
│   ├── encryption.md
│   ├── secrets-management.md
│   ├── kafka-security.md
│   └── kubernetes-hardening.md
│
├── 09-resilience/
│   ├── rate-limiting.md
│   ├── fail-fast.md
│   ├── circuit-breakers.md
│   ├── fallback-strategy.md
│   ├── chaos-engineering.md
│   └── timeout-strategy.md
│
├── 10-strategy/
│   ├── team-structure.md
│   ├── operating-model.md
│   ├── roadmap.md
│   ├── support-model.md
│   ├── knowledge-management.md
│   └── escalation-model.md
│
├── 11-adr/
│   ├── ADR-001-event-driven-architecture.md
│   ├── ADR-002-kafka-as-backbone.md
│   ├── ADR-003-webflux-over-mvc.md
│   ├── ADR-004-risk-engine-rules-first.md
│   ├── ADR-005-mtls-and-jwt.md
│   ├── ADR-006-kubernetes.md
│   └── ADR-007-hybrid-risk-engine.md
│
├── 12-mvp/
│   ├── mvp-scope.md
│   ├── implementation-roadmap.md
│   ├── phase-1-deliverables.md
│   ├── phase-2-deliverables.md
│   └── future-evolution.md
│
├── diagrams/
│   ├── png/
│   ├── pdf/
│   └── drawio/
│
└── appendix/
    ├── glossary.md
    ├── assumptions.md
    ├── references.md
    └── capacity-calculations.md