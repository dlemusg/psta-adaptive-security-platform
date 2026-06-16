# ADR-006: Kubernetes como Plataforma de Ejecución


## Contexto

La Plataforma de Seguridad Transaccional Adaptativa (PSTA) está diseñada bajo una arquitectura basada en:

```text
Microservicios
+
Event-Driven Architecture
+
Kafka
+
Programación Reactiva
```

Los servicios principales incluyen:

```text
Risk Assessment Service
Adaptive Authentication Service
Transaction Decision Service
Account Protection Service
Customer Profile Service
Device Trust Service
Audit Service
```

La plataforma debe soportar:

- Alta disponibilidad.
- Escalabilidad horizontal.
- Recuperación automática ante fallos.
- Despliegues continuos.
- Canary Releases.
- Multi-Región.
- Observabilidad centralizada.

Adicionalmente, la solución debe ejecutarse sobre AWS como plataforma cloud corporativa.

---

## Problema

La arquitectura propuesta requiere una plataforma capaz de administrar:

```text
Contenedores
Escalabilidad
Networking
Configuración
Seguridad
Observabilidad
Despliegues
```

sin incrementar significativamente la complejidad operativa.

Las alternativas evaluadas presentan limitaciones para una solución distribuida y altamente escalable.

---

## Alternativas Evaluadas

### Alternativa 1: Máquinas Virtuales

```text
EC2
   ↓
Microservicios
```

---

### Ventajas

- Simplicidad conceptual.
- Amplia adopción.

---

### Desventajas

#### Escalabilidad Manual

```text
Provisionar VM
Configurar VM
Desplegar Aplicación
```

---

#### Mayor Costo Operativo

Mayor administración de infraestructura.

---

#### Baja Elasticidad

No responde dinámicamente a cambios de carga.

---

### Resultado

**Descartada.**

---

## Alternativa 2: ECS Fargate

```text
Containers
     ↓
AWS ECS
```

---

### Ventajas

- Menor complejidad.
- Servicio administrado.

---

### Desventajas

#### Menor Portabilidad

Mayor dependencia de AWS.

---

#### Menor Flexibilidad

Capacidades limitadas frente a Kubernetes.

---

### Resultado

**Descartada.**

---

## Alternativa 3: Kubernetes

```text
Containers
      ↓
Kubernetes
```

---

### Ventajas

- Estándar de industria.
- Portabilidad.
- Escalabilidad.
- Ecosistema maduro.
- Compatibilidad Cloud Native.

---

### Resultado

**Seleccionada.**

---

## Decisión

Se adopta Kubernetes como plataforma principal de ejecución para todos los microservicios de la solución.

La implementación administrada será:

```text
Amazon EKS
(Elastic Kubernetes Service)
```

---

# Arquitectura Resultante

```text
AWS EKS Cluster

├── Risk Assessment Service
├── Adaptive Authentication Service
├── Transaction Decision Service
├── Account Protection Service
├── Device Trust Service
├── Customer Profile Service
└── Audit Service
```

---

# Justificación Técnica

## 1. Escalabilidad Horizontal

Cada servicio puede escalar independientemente.

Ejemplo:

```text
Risk Assessment Service
       ↓
10 Pods
```

---

Mientras:

```text
Audit Service
      ↓
2 Pods
```

---

## 2. Auto Healing

Si un contenedor falla:

```text
Pod Failure
      ↓
Kubernetes
      ↓
Nuevo Pod
```

sin intervención manual.

---

## 3. Alta Disponibilidad

Los pods se distribuyen entre múltiples nodos.

```text
Node A
Node B
Node C
```

---

La pérdida de un nodo no implica indisponibilidad del servicio.

---

## 4. Despliegues Seguros

Kubernetes soporta:

```text
Rolling Updates
Canary Releases
Blue-Green Deployments
```

---

## 5. Cloud Native

La plataforma se integra naturalmente con:

```text
Kafka
Prometheus
Grafana
OpenTelemetry
Istio
```

---

## Beneficios

### Elasticidad

La capacidad crece y disminuye automáticamente.

---

### Resiliencia

Los servicios se recuperan automáticamente.

---

### Portabilidad

La solución no queda acoplada a un proveedor específico.

---

### Observabilidad

Permite instrumentación homogénea.

---

### Automatización

Facilita CI/CD y GitOps.

---

# Arquitectura de Clúster

## Región Primaria

```text
AWS Region A

EKS Cluster
```

---

## Nodos

```text
Worker Node 1
Worker Node 2
Worker Node 3
```

---

## Namespaces

```text
fraud-platform
monitoring
observability
```

---

# Estrategia de Escalamiento

## Horizontal Pod Autoscaler (HPA)

Cada servicio puede escalar automáticamente.

Ejemplo:

```yaml
Min Pods: 2
Max Pods: 20
```

---

## Métricas

```text
CPU
Memoria
Kafka Lag
TPS
```

---

## Caso de Riesgo

```text
Aumento de transacciones
        ↓
Más eventos Kafka
        ↓
Escalamiento automático
```

---

# Estrategia de Disponibilidad

Todos los servicios críticos operan con:

```text
Mínimo 2 Pods
```

---

Ejemplo:

```text
Risk Assessment Service

Pod A
Pod B
```

---

# Gestión de Configuración

## ConfigMaps

Utilizados para:

```text
Kafka Topics
URLs
Parámetros de negocio
```

---

## Secrets

Utilizados para:

```text
Passwords
Tokens
Certificados
```

---

Integrados con:

```text
AWS Secrets Manager
```

---

# Estrategia de Networking

Cada microservicio expone:

```text
ClusterIP
```

---

El ingreso externo se realiza mediante:

```text
API Gateway
```

---

No se exponen servicios internos a Internet.

---

# Estrategia de Observabilidad

## Métricas

```text
Prometheus
```

---

## Dashboards

```text
Grafana
```

---

## Logs

```text
OpenSearch
```

---

## Tracing

```text
OpenTelemetry
```

---

# Estrategia de Seguridad

## Comunicación Externa

```text
TLS
JWT
```

---

## Comunicación Interna

```text
mTLS
```

---

## Gestión de Secretos

```text
AWS Secrets Manager
```

---

## Principio

```text
Zero Trust
```

---

# Estrategia de Despliegue

La plataforma utiliza:

```text
Azure DevOps
+
Azure Pipelines
+
AWS EKS
```

---

## Flujo

```text
Azure Repos
      ↓
Azure Pipeline
      ↓
AWS ECR
      ↓
Helm Deploy
      ↓
AWS EKS
```

---

# Estrategia Multi-Región

La arquitectura contempla:

```text
Primary Region
+
Secondary Region
```

---

## Beneficios

### Continuidad de Negocio

Protección ante desastres regionales.

---

### Recuperación

Menor RTO y RPO.

---

### Escalabilidad Global

Capacidad de crecimiento futuro.

---

# Consecuencias Positivas

### Escalabilidad

Incremento automático de capacidad.

---

### Alta Disponibilidad

Reducción de puntos únicos de falla.

---

### Automatización

Menor intervención operativa.

---

### Resiliencia

Recuperación automática ante fallos.

---

### Compatibilidad Cloud Native

Integración natural con el ecosistema moderno.

---

# Consecuencias Negativas

### Complejidad Operacional

Kubernetes requiere conocimientos especializados.

---

### Curva de Aprendizaje

Los equipos deben comprender:

```text
Pods
Deployments
Services
Ingress
HPA
Namespaces
```

---

### Costos de Observabilidad

Es necesario monitorear adecuadamente la plataforma.

---

# Relación con Otros ADR

Esta decisión soporta:

- ADR-001: Event-Driven Architecture.
- ADR-002: Kafka as Backbone.
- ADR-003: WebFlux over MVC.
- ADR-005: mTLS and JWT.
- ADR-007: Hybrid Risk Engine.

---

# Impacto en la Arquitectura

Todos los microservicios serán desplegados como contenedores Docker ejecutándose sobre AWS EKS.

La plataforma utilizará:

```text
Deployments
Services
ConfigMaps
Secrets
HPA
Ingress
```

como componentes estándar de ejecución.

---

# Decisión Final

Se adopta Kubernetes, implementado mediante Amazon EKS, como plataforma principal de ejecución para la solución.

La decisión se fundamenta en su capacidad para proporcionar escalabilidad horizontal, alta disponibilidad, resiliencia operativa, automatización de despliegues y compatibilidad con arquitecturas modernas basadas en microservicios y eventos, alineándose además con la estrategia cloud de AWS y las prácticas DevOps definidas para la plataforma.