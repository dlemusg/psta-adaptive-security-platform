# Escenario 2: Transferencia desde un Dispositivo Nuevo

## Objetivo

Validar el comportamiento de la plataforma cuando un cliente inicia una transacción desde un dispositivo no reconocido previamente, aplicando autenticación adaptativa para reducir el riesgo de fraude sin bloquear innecesariamente la operación.

---

# Contexto

Un cliente intenta realizar una transferencia utilizando un dispositivo que no ha sido registrado anteriormente en la plataforma.

Aunque el comportamiento transaccional parece normal, la falta de confianza sobre el dispositivo incrementa el nivel de riesgo y requiere una validación adicional de identidad.

---

# Precondiciones

## Cliente

- Cuenta activa.
- Sin bloqueos.
- Sin restricciones operativas.

## Dispositivo

- No registrado previamente.
- Sin historial de confianza.
- Device Trust desconocido.

## Beneficiario

- Beneficiario conocido.

## Comportamiento

- Monto habitual.
- Ubicación habitual.
- Frecuencia normal.

---

# Diagrama de Secuencia

El detalle técnico completo del escenario puede consultarse en el siguiente diagrama de secuencia:

![Escenario 2 - Dispositivo Nuevo](sequence-diagrams\scenario-2-new-device.png)

---

# Flujo Principal

## Paso 1

El cliente inicia una transferencia desde un dispositivo no registrado.

```text
Canal Digital
    ↓
Transaction Decision Service
```

---

## Paso 2

El sistema publica:

```text
TransactionReceived
```

---

## Paso 3

Risk Assessment Service recibe el evento y evalúa:

- Perfil del cliente.
- Velocidad transaccional.
- Ubicación.
- Beneficiario.
- Dispositivo.

---

## Paso 4

Device Risk Engine identifica que el dispositivo no posee historial de confianza.

El motor de riesgo calcula:

```text
RiskScore = 0.65
RiskLevel = HIGH
```

y publica:

```text
RiskScoreCalculated
```

---

## Paso 5

Transaction Decision Service recibe el resultado.

La política de negocio determina que las operaciones de alto riesgo requieren autenticación reforzada.

Se publica:

```text
TransactionRequiresAuthentication
```

---

## Paso 6

Adaptive Authentication Service recibe la solicitud y selecciona el mecanismo de autenticación más apropiado.

Por ejemplo:

```text
Push Authentication
```

o

```text
OTP
```

según disponibilidad y contexto.

---

## Paso 7

El cliente completa exitosamente la autenticación.

Se publica:

```text
AuthenticationCompleted
```

---

## Paso 8

Transaction Decision Service recibe el resultado exitoso de autenticación.

La operación es aprobada y se publica:

```text
TransactionApproved
```

---

## Paso 9

Core Bancario ejecuta la transferencia.

---

## Paso 10

El dispositivo es marcado como confiable para futuras operaciones.

Se publica:

```text
DeviceTrustGranted
```

---

# Eventos Generados

## Publicados

```text
TransactionReceived
RiskScoreCalculated
TransactionRequiresAuthentication
AuthenticationCompleted
TransactionApproved
DeviceTrustGranted
```

---

## Consumidos

```text
TransactionReceived
RiskScoreCalculated
TransactionRequiresAuthentication
AuthenticationCompleted
```

---

# Decisiones Tomadas

| Regla | Resultado |
|---------|------------|
| Dispositivo conocido | No |
| Beneficiario conocido | Sí |
| Ubicación habitual | Sí |
| Monto habitual | Sí |
| Riesgo alto | Sí |
| Requiere autenticación adaptativa | Sí |
| Autenticación exitosa | Sí |

---

# Resultado Esperado

La transferencia es aprobada únicamente después de validar exitosamente la identidad del cliente mediante autenticación adaptativa.

---

# Beneficios para el Negocio

| Beneficio para el Negocio | Objetivo Principal | Impacto de la Solución |
| :--- | :--- | :--- |
| **Reducción de Fraude** | Detección proactiva | Permite detectar intentos potenciales de Account Takeover utilizando dispositivos desconocidos. |
| **Experiencia de Usuario** | Continuidad del cliente | Se evita bloquear automáticamente al cliente legítimo. |
| **Adaptabilidad** | Controles dinámicos | La plataforma aplica controles únicamente cuando existe una señal de riesgo relevante. |
| **Construcción de Confianza** | Historial de reputación | El dispositivo puede incorporarse posteriormente al historial de confianza del cliente. |

---

# Atributos de Calidad Involucrados

| Atributo de Calidad | Objetivo | Enfoque Arquitectónico |
| :--- | :--- | :--- |
| **Seguridad** | Validación de identidad | La identidad del cliente es validada de forma rigurosa antes de aprobar la operación. |
| **Resiliencia** | Alta disponibilidad | La autenticación puede utilizar múltiples factores de respaldo dependiendo de la disponibilidad de proveedores. |
| **Auditabilidad** | Trazabilidad del proceso | Todo el flujo de validación queda registrado de forma inmutable mediante eventos de dominio. |
| **Escalabilidad** | Desacoplamiento de componentes | La evaluación de riesgo y la autenticación se ejecutan mediante servicios independientes y autónomos. |

---

# Relación con la Arquitectura

## Servicios Participantes

```text
Canal Digital
API Gateway
Transaction Decision Service
Kafka
Risk Assessment Service
Adaptive Authentication Service
Device Trust Service
Core Banking
Audit Service
```

---

## Componentes Clave

| Componente Clave | Función / Responsabilidad |
| :--- | :--- |
| **Device Risk Engine** | Detecta dispositivos nuevos o desconocidos. |
| **Risk Score Calculator** | Calcula el nivel de riesgo asociado a la operación. |
| **Factor Selection Policy** | Selecciona el método de autenticación más adecuado. |
| **Authentication Orchestrator** | Gestiona el flujo completo de autenticación adaptativa. |
| **Decision Engine** | Determina si la operación puede continuar. |

---

# Diferencias respecto al Escenario 1

| Aspecto | Escenario 1 | Escenario 2 |
|----------|------------|------------|
| Dispositivo | Conocido | Nuevo |
| Riesgo | LOW | HIGH |
| Autenticación adicional | No | Sí |
| Device Trust | Existente | Se construye |
| Fricción para el usuario | Mínima | Moderada |

---



Este escenario demuestra la capacidad de la plataforma para identificar dispositivos desconocidos y aplicar autenticación adaptativa de manera dinámica. La solución incrementa la seguridad ante posibles intentos de apropiación de cuenta sin afectar innecesariamente la experiencia de los clientes legítimos.