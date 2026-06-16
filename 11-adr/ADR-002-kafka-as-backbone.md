# ADR-002: Kafka como Backbone de Eventos


## Contexto

La Plataforma de Seguridad Transaccional Adaptativa (PSTA) adopta una arquitectura orientada a eventos para soportar:

- Evaluación continua de riesgo.
- Autenticación adaptativa.
- Protección de cuentas.
- Correlación de señales de fraude.
- Auditoría de operaciones.
- Escalabilidad horizontal.

Como consecuencia de la decisión establecida en el ADR-001, se requiere una plataforma de mensajería capaz de actuar como backbone de integración entre bounded contexts.

Los principales dominios involucrados son:

```text
Risk Assessment
Adaptive Authentication
Transaction Decision
Account Protection
Device Trust
Customer Profile
Audit
```

La plataforma debe soportar:

```text
Alta disponibilidad
Alta concurrencia
Persistencia de eventos
Reprocesamiento
Escalabilidad horizontal
Ordenamiento por clave
Trazabilidad
```

---

## Problema

Los dominios requieren intercambiar eventos de forma desacoplada y resiliente.

Las alternativas tradicionales presentan limitaciones.

### REST Síncrono

```text
Service A
    ↓
Service B
```

Problemas:

- Acoplamiento temporal.
- Cascading failures.
- Latencia acumulada.
- Baja resiliencia.

---

### Colas Tradicionales

Ejemplos:

```text
RabbitMQ
AWS SQS
ActiveMQ
```

Aunque permiten comunicación asíncrona, presentan limitaciones para el caso de uso.

Problemas:

- Orientadas a entrega.
- Reprocesamiento limitado.
- Retención reducida.
- Menor capacidad de replay.
- Menor soporte para streaming continuo.

---

### Base de Datos Compartida

```text
Service A
    ↓
Shared Database
    ↓
Service B
```

Problemas:

- Alto acoplamiento.
- Dependencia de esquema.
- Baja autonomía de dominios.

---

## Decisión

Se adopta Apache Kafka como backbone principal de eventos para toda la plataforma.

La implementación administrada será:

```text
AWS MSK
(Managed Streaming for Kafka)
```

---

## Principios Adoptados

### Event Streaming

Los eventos representan hechos del negocio.

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

### Event Retention

Los eventos permanecen disponibles durante un periodo configurable.

Ejemplo:

```text
7 días
30 días
90 días
```

según criticidad del tópico.

---

### Replay Capability

Los consumidores pueden reprocesar eventos históricos.

Ejemplo:

```text
Reentrenamiento ML
Investigación de fraude
Recálculo de riesgo
```

---

### Consumer Independence

Cada dominio consume eventos a su propio ritmo.

```text
Producer
     ↓
Kafka
     ↓
Consumer A

Consumer B

Consumer C
```

---

### Horizontal Scalability

El procesamiento puede escalar mediante particiones.

```text
Topic
 ├── Partition 1
 ├── Partition 2
 ├── Partition 3
 └── Partition N
```

---

## Arquitectura Resultante

```text
Transaction Decision
           │
           ▼
TransactionReceived
           │
           ▼
      Kafka/MSK
           │
 ┌─────────┼─────────┐
 ▼         ▼         ▼

Risk     Audit     Analytics
Service  Service   Service
```

---

# Justificación Técnica

## 1. Alto Throughput

Kafka fue diseñado para procesar millones de eventos por segundo.

La plataforma debe soportar:

```text
Transferencias
Pagos
Autenticaciones
Eventos de riesgo
Eventos de auditoría
```

de forma concurrente.

---

## 2. Persistencia de Eventos

A diferencia de una cola tradicional:

```text
Mensaje consumido
↓
Mensaje eliminado
```

Kafka conserva los eventos.

```text
Evento consumido
↓
Evento permanece
```

---

## 3. Reprocesamiento

Permite:

```text
Replay
Rebuild
Backfill
Reconciliation
```

sin afectar productores.

---

## 4. Escalabilidad Horizontal

Los consumidores escalan mediante Consumer Groups.

```text
Risk Assessment

Consumer 1
Consumer 2
Consumer 3
```

procesando particiones en paralelo.

---

## 5. Ordenamiento

Kafka garantiza orden dentro de una partición.

Ejemplo:

```text
Transaction A
Transaction B
Transaction C
```

manteniendo secuencia para una misma clave.

---

## Beneficios

### Desacoplamiento

Los productores desconocen quién consume los eventos.

---

### Resiliencia

Los consumidores pueden detenerse temporalmente sin pérdida de información.

---

### Escalabilidad

La capacidad puede incrementarse agregando:

```text
Partitions
Consumers
Brokers
```

---

### Auditabilidad

Los eventos representan el historial completo de decisiones.

---

### Evolución

Nuevos consumidores pueden añadirse sin modificar productores.

Ejemplo:

```text
Machine Learning
Fraud Analytics
Compliance
Data Lake
```

---

## Consecuencias Positivas

### Backbone Único

Toda la integración de negocio ocurre sobre la misma plataforma.

---

### Soporte Natural para CQRS

Los eventos alimentan modelos de lectura especializados.

---

### Integración con Machine Learning

Los eventos históricos pueden utilizarse para entrenamiento de modelos.

---

### Event Replay

Permite reconstrucción de estados.

---

### Escalabilidad Organizacional

Nuevos equipos pueden consumir eventos existentes.

---

## Consecuencias Negativas

### Complejidad Operacional

Kafka requiere:

```text
Monitoreo
Observabilidad
Capacity Planning
```

---

### Gestión de Particiones

Debe definirse correctamente la estrategia de particionado.

---

### Gestión de Contratos

Los eventos deben mantenerse compatibles entre versiones.

---

### Eventual Consistency

La consistencia entre dominios no es inmediata.

---

## Estrategia de Tópicos

La plataforma utiliza tópicos orientados a dominio.

Ejemplos:

```text
transaction-events
risk-events
authentication-events
account-protection-events
audit-events
```

---

## Estrategia de Particionado

La clave principal será:

```text
customerId
```

o

```text
accountId
```

según el evento.

---

### Beneficio

Garantiza:

```text
Orden por cliente
Orden por cuenta
Consistencia temporal
```

---

## Estrategia de Contratos

Los eventos serán versionados.

Ejemplo:

```text
RiskScoreCalculated.v1
RiskScoreCalculated.v2
```

---

## Estrategia de Serialización

Se adopta:

```text
Apache Avro
+
Schema Registry
```

---

### Beneficios

- Compatibilidad.
- Evolución controlada.
- Menor tamaño de mensaje.
- Validación automática.

---

## Estrategia de Entrega

La plataforma adopta:

```text
At-Least-Once Delivery
```

---

### Implicaciones

Los consumidores deben ser:

```text
Idempotentes
```

para tolerar reprocesamiento.

---

## Alternativas Evaluadas

### RabbitMQ

#### Ventajas

- Fácil adopción.
- Menor complejidad.

#### Desventajas

- Menor capacidad de replay.
- Menor throughput.
- Menor orientación a streaming.

**Descartada.**

---

### AWS SQS

#### Ventajas

- Servicio administrado.

#### Desventajas

- No orientado a streaming.
- Sin replay avanzado.
- Menor flexibilidad para múltiples consumidores.

**Descartada.**

---

### ActiveMQ

#### Ventajas

- Integración JMS.

#### Desventajas

- Menor escalabilidad.
- Menor adopción moderna.

**Descartada.**

---

## Relación con Otros ADR

Esta decisión soporta:

- ADR-001: Event-Driven Architecture.
- ADR-003: WebFlux sobre Spring MVC.
- ADR-004: Risk Engine Rules First.
- ADR-007: Hybrid Risk Engine.

---

## Impacto en la Arquitectura

Kafka se convierte en el principal mecanismo de integración entre bounded contexts.

Todos los eventos críticos del negocio serán publicados y consumidos mediante Kafka.

La plataforma utilizará:

```text
AWS MSK
Apache Avro
Schema Registry
Consumer Groups
Dead Letter Topics
Retry Topics
```

para garantizar resiliencia y escalabilidad.

---

## Decisión Final

Se adopta Apache Kafka, implementado mediante AWS MSK, como backbone principal de eventos de la plataforma.

La decisión se fundamenta en su capacidad de proporcionar streaming distribuido, persistencia de eventos, escalabilidad horizontal, replay de información histórica y desacoplamiento entre dominios, características fundamentales para una plataforma moderna de prevención de fraude y autenticación adaptativa.