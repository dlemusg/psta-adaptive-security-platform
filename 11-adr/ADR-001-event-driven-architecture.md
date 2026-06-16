# ADR-001: Adopción de Arquitectura Orientada a Eventos


## Contexto

La Plataforma de Seguridad Transaccional Adaptativa (PSTA) debe procesar eventos financieros en tiempo real para detectar comportamientos fraudulentos, aplicar autenticación adaptativa y tomar decisiones transaccionales con baja latencia.

Los requerimientos funcionales y no funcionales del reto incluyen:

- Evaluación continua del riesgo.
- Correlación de múltiples señales de fraude.
- Procesamiento en tiempo real.
- Escalabilidad horizontal.
- Alta disponibilidad.
- Resiliencia ante fallos parciales.
- Integración de múltiples dominios de negocio.

Los principales bounded contexts identificados son:

```text
Risk Assessment
Adaptive Authentication
Transaction Decision
Account Protection
Customer Profile
Device Trust
Audit
```

Estos dominios necesitan intercambiar información de forma desacoplada y reaccionar a eventos generados por otros contextos.

---

## Problema

Una arquitectura basada exclusivamente en comunicación síncrona REST presenta limitaciones importantes.

### Acoplamiento Temporal

Los servicios dependen de la disponibilidad inmediata de otros servicios.

```text
Service A
    ↓
Service B
    ↓
Service C
```

Una falla en cualquiera de los componentes afecta toda la cadena.

---

### Escalabilidad Limitada

Las llamadas síncronas generan:

- Mayor consumo de recursos.
- Bloqueo de hilos.
- Incremento de latencia acumulada.

---

### Baja Resiliencia

Una indisponibilidad temporal puede provocar:

```text
Timeouts
Retries
Cascading Failures
```

---

### Dificultad para Procesamiento Asíncrono

Casos como:

```text
Fraud Detection
Risk Scoring
Audit Logging
Behavior Analytics
```

no requieren respuesta inmediata al consumidor.

---

## Decisión

Se adopta una arquitectura orientada a eventos (Event-Driven Architecture - EDA) como mecanismo principal de integración entre bounded contexts.

La comunicación entre dominios se realizará mediante eventos de negocio publicados sobre una plataforma de streaming distribuida.

---

## Principios Adoptados

### Event-Carried State Transfer

Los eventos contienen la información necesaria para que otros dominios reaccionen sin necesidad de realizar consultas síncronas adicionales.

Ejemplo:

```json
{
  "eventType": "RiskScoreCalculated",
  "transactionId": "TX-123",
  "riskScore": 0.82,
  "riskLevel": "HIGH"
}
```

---

### Publicación de Eventos de Dominio

Cada bounded context publica eventos que representan hechos relevantes del negocio.

Ejemplos:

```text
TransactionReceived
RiskScoreCalculated
AuthenticationCompleted
TransactionApproved
TransactionRejected
AccountBlocked
```

---

### Consumo Asíncrono

Los consumidores procesan eventos de forma desacoplada.

```text
Producer
    ↓
Kafka
    ↓
Consumer
```

---

### Eventual Consistency

La consistencia entre dominios se logra mediante propagación de eventos.

No se requiere consistencia distribuida mediante transacciones XA.

---

## Arquitectura Resultante

```text
Transaction Decision
          │
          ▼
TransactionReceived
          │
          ▼
Kafka
          │
          ▼
Risk Assessment
          │
          ▼
RiskScoreCalculated
          │
          ▼
Adaptive Authentication
          │
          ▼
AuthenticationCompleted
          │
          ▼
Transaction Decision
```

---

## Beneficios

### Desacoplamiento

Los servicios no conocen detalles internos de otros dominios.

---

### Escalabilidad

Cada consumidor puede escalar independientemente.

```text
Risk Assessment x10
Authentication x5
Audit x20
```

---

### Resiliencia

Los eventos permanecen disponibles incluso si un consumidor está temporalmente fuera de servicio.

---

### Extensibilidad

Nuevos consumidores pueden suscribirse a eventos existentes sin modificar productores.

Ejemplo:

```text
Fraud Analytics
Machine Learning
Compliance
Data Lake
```

---

### Auditabilidad

Los eventos representan hechos históricos del negocio.

Facilitan:

- Auditoría.
- Investigación de fraude.
- Reprocesamiento.
- Trazabilidad.

---

## Consecuencias Positivas

### Separación de Responsabilidades

Cada bounded context mantiene autonomía.

---

### Procesamiento en Tiempo Real

Los eventos son propagados inmediatamente.

---

### Soporte para CQRS

La arquitectura habilita naturalmente modelos de lectura especializados.

---

### Evolución hacia Capacidades Predictivas

Los eventos pueden ser consumidos posteriormente por motores de Machine Learning.

---

## Consecuencias Negativas

### Mayor Complejidad Operacional

Se requiere infraestructura adicional:

```text
Kafka
Monitoring
Schema Registry
```

---

### Eventual Consistency

Los cambios no son inmediatamente visibles en todos los dominios.

---

### Gestión de Versionado

Los eventos deben evolucionar manteniendo compatibilidad.

---

### Observabilidad Distribuida

Es necesario implementar:

```text
Tracing
Correlation IDs
Distributed Logging
```

---

## Alternativas Evaluadas

### Arquitectura Monolítica

#### Ventajas

- Menor complejidad inicial.
- Desarrollo más simple.

#### Desventajas

- Escalabilidad limitada.
- Alto acoplamiento.
- Evolución difícil.

**Descartada.**

---

### Arquitectura Basada Exclusivamente en REST

#### Ventajas

- Simplicidad conceptual.
- Fácil adopción.

#### Desventajas

- Acoplamiento temporal.
- Menor resiliencia.
- Escalabilidad limitada.

**Descartada.**

---

### Arquitectura Híbrida

Combina:

```text
REST
+
Eventos
```

#### Resultado

Esta alternativa fue seleccionada parcialmente.

La plataforma utiliza:

### REST

Para:

```text
Canales Digitales
APIs Externas
Consultas
```

### Eventos

Para:

```text
Procesamiento de Riesgo
Autenticación
Protección de Cuenta
Auditoría
```

---

## Impacto en la Arquitectura

### Bounded Contexts

Los contextos interactúan mediante eventos de dominio y mantienen autonomía funcional.

---

### Microservicios

Cada microservicio puede evolucionar y desplegarse de forma independiente.

---

### Escalabilidad

Los consumidores pueden escalar horizontalmente sin afectar productores.

---

### Resiliencia

La plataforma tolera fallos parciales sin comprometer el procesamiento global.

---

### Observabilidad

Cada evento incorpora:

```text
CorrelationId
TraceId
Timestamp
Source
EventVersion
```

para garantizar trazabilidad extremo a extremo.

---

## Relación con Otros ADR

Este ADR fundamenta las siguientes decisiones:

- ADR-002: Kafka como Backbone de Eventos.
- ADR-003: WebFlux sobre Spring MVC.
- ADR-006: Kubernetes como Plataforma de Ejecución.
- ADR-007: Motor Híbrido de Riesgo.

---

## Decisión Final

Se adopta una arquitectura orientada a eventos como mecanismo principal de integración entre bounded contexts.

La solución utilizará eventos de dominio para garantizar desacoplamiento, resiliencia, escalabilidad y capacidad de evolución futura, manteniendo REST únicamente para interacciones síncronas necesarias con canales digitales, APIs externas y consultas de información.