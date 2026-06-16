# Interacciones entre Contextos

## Propósito

Este documento describe las relaciones entre los Bounded Contexts de la Plataforma de Seguridad Transaccional Adaptativa (PSTA).

Su objetivo es:

- Identificar dependencias entre dominios.
- Definir responsabilidades.
- Minimizar acoplamientos.
- Establecer mecanismos de integración.
- Facilitar la evolución independiente de cada contexto.

---

# Principios

Las interacciones entre contextos deben cumplir los siguientes principios:

- Comunicación basada en eventos siempre que sea posible.
- Bajo acoplamiento entre dominios.
- Autonomía de los Bounded Contexts.
- Consistencia eventual entre contextos.
- Contratos explícitos mediante eventos de dominio.

---

# Mapa General de Interacciones

```text
                   ┌─────────────────────┐
                   │ Perfil de Cliente   │
                   └──────────┬──────────┘
                              │
                              ▼
┌────────────────────────────────────────────────┐
│ Monitoreo de Riesgo y Fraude                   │
└───────────────┬───────────────────┬────────────┘
                │                   │
                ▼                   ▼
      ┌────────────────┐   ┌──────────────────┐
      │ Dispositivos   │   │ Protección Cuenta│
      └────────────────┘   └──────────────────┘
                │
                ▼
      ┌────────────────────┐
      │ Autenticación      │
      │ Adaptativa         │
      └─────────┬──────────┘
                │
                ▼
      ┌────────────────────┐
      │ Proveedores        │
      │ de Factores        │
      └────────────────────┘

                ▲
                │
      ┌────────────────────┐
      │ Decisión           │
      │ Transaccional      │
      └────────────────────┘

                │
                ▼

      ┌────────────────────┐
      │ Auditoría          │
      │ y Evidencia        │
      └────────────────────┘
```

---

# Contexto: Decisión Transaccional

## Responsabilidad

Determinar el resultado final de una operación financiera.

### Publica

| Evento |
|----------|
| TransactionReceived |
| TransactionApproved |
| TransactionRejected |
| TransactionRequiresAuthentication |

### Consume

| Evento | Productor |
|----------|------------|
| RiskScoreCalculated | Monitoreo de Riesgo |
| AuthenticationCompleted | Autenticación Adaptativa |
| AuthenticationFailed | Autenticación Adaptativa |
| AccountBlocked | Protección de Cuenta |

---

## Relación con Monitoreo de Riesgo

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Customer / Supplier |
| Upstream | Monitoreo de Riesgo |
| Downstream | Decisión Transaccional |
| Integración | Eventos Kafka |
| Contrato | RiskScoreCalculated |

---

## Relación con Autenticación Adaptativa

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Customer / Supplier |
| Upstream | Autenticación Adaptativa |
| Downstream | Decisión Transaccional |
| Integración | Eventos Kafka |
| Contrato | AuthenticationCompleted |

---

# Contexto: Monitoreo de Riesgo y Fraude

## Responsabilidad

Evaluar el riesgo asociado a una operación y detectar patrones de fraude.

### Publica

| Evento |
|----------|
| RiskScoreCalculated |
| FraudPatternDetected |
| CriticalRiskDetected |

### Consume

| Evento |
|----------|
| TransactionReceived |
| DeviceTrustGranted |
| DeviceTrustRevoked |
| AuthenticationCompleted |

---

## Relación con Perfil de Cliente

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Customer / Supplier |
| Upstream | Perfil de Cliente |
| Downstream | Riesgo |
| Integración | Eventos |
| Información | Historial transaccional |

---

## Relación con Gestión de Dispositivos

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Customer / Supplier |
| Upstream | Gestión de Dispositivos |
| Downstream | Riesgo |
| Integración | Eventos |
| Información | Device Trust |

---

## Relación con Protección de Cuenta

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Published Language |
| Integración | Kafka |
| Eventos | FraudPatternDetected, CriticalRiskDetected |

---

# Contexto: Autenticación Adaptativa

## Responsabilidad

Determinar y ejecutar el mecanismo de autenticación apropiado según el riesgo.

### Publica

| Evento |
|----------|
| AuthenticationRequested |
| AuthenticationCompleted |
| AuthenticationFailed |

### Consume

| Evento |
|----------|
| RiskScoreCalculated |
| TransactionRequiresAuthentication |

---

## Relación con Riesgo

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Conformist |
| Upstream | Monitoreo de Riesgo |
| Downstream | Autenticación |
| Contrato | RiskScoreCalculated |

---

## Relación con Proveedores de Factores

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Anti-Corruption Layer |
| Integración | Adaptadores |
| Objetivo | Aislar dependencias externas |

---

# Contexto: Gestión de Dispositivos

## Responsabilidad

Gestionar la confianza y reputación de los dispositivos.

### Publica

| Evento |
|----------|
| DeviceRegistered |
| DeviceTrustGranted |
| DeviceTrustRevoked |

### Consume

| Evento |
|----------|
| AuthenticationCompleted |
| RiskEvaluationRequested |

---

## Relación con Riesgo

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Published Language |
| Integración | Eventos |
| Información Compartida | Device Trust |

---

# Contexto: Perfil de Cliente

## Responsabilidad

Mantener el comportamiento histórico y contexto del cliente.

### Publica

| Evento |
|----------|
| CustomerProfileUpdated |

### Consume

| Evento |
|----------|
| TransactionApproved |
| AuthenticationCompleted |

---

## Relación con Riesgo

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Open Host Service |
| Consumidor | Monitoreo de Riesgo |
| Información | Perfil histórico |

---

# Contexto: Protección de Cuenta

## Responsabilidad

Aplicar acciones de mitigación ante riesgos críticos.

### Publica

| Evento |
|----------|
| AccountBlocked |
| AccountRestricted |
| AccountUnblocked |

### Consume

| Evento |
|----------|
| FraudPatternDetected |
| CriticalRiskDetected |

---

## Relación con Riesgo

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Customer / Supplier |
| Upstream | Riesgo |
| Downstream | Protección de Cuenta |

---

# Contexto: Proveedores de Factores

## Responsabilidad

Ejecutar mecanismos concretos de autenticación.

### Publica

| Evento |
|----------|
| FactorExecuted |
| FactorFailed |

### Consume

| Evento |
|----------|
| AuthenticationRequested |

---

## Relación con Autenticación Adaptativa

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Anti-Corruption Layer |
| Objetivo | Desacoplar proveedores externos |
| Integración | Adaptadores |

---

# Contexto: Auditoría y Evidencia

## Responsabilidad

Garantizar trazabilidad completa de decisiones y eventos.

### Consume

| Evento |
|----------|
| TransactionReceived |
| TransactionApproved |
| TransactionRejected |
| RiskScoreCalculated |
| AuthenticationCompleted |
| AuthenticationFailed |
| FraudPatternDetected |
| AccountBlocked |

---

## Relación con Todos los Contextos

| Tipo | Valor |
|---------|---------|
| Patrón DDD | Subscriber |
| Integración | Kafka |
| Persistencia | Event Store / Auditoría |

---

# Patrones Estratégicos Utilizados

| Patrón | Aplicación |
|----------|------------|
| Customer / Supplier | Riesgo → Decisión Transaccional |
| Customer / Supplier | Riesgo → Protección de Cuenta |
| Conformist | Autenticación → Riesgo |
| Published Language | Eventos Kafka |
| Open Host Service | Perfil de Cliente |
| Anti-Corruption Layer | Proveedores Externos |
| Event Driven Collaboration | Todos los contextos |

---

# Estrategia de Integración

## Comunicación Asíncrona

La comunicación principal entre contextos se realiza mediante:

```text
Apache Kafka
```

Utilizando:

- Eventos de dominio.
- Contratos versionados.
- Consistencia eventual.

---

## Comunicación Sincrónica

Se permite únicamente para:

- Consultas de referencia.
- Configuración.
- Catálogos.

Nunca para decisiones críticas de fraude.

---

# Principio Rector

Los Bounded Contexts deben evolucionar de manera independiente, compartiendo únicamente eventos de negocio y evitando dependencias técnicas directas.

La comunicación basada en eventos permite una plataforma resiliente, escalable y alineada con los principios de Domain-Driven Design y Event-Driven Architecture.