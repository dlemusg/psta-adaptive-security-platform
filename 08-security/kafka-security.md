# Kafka Security

## Propósito

Este documento describe los controles de seguridad aplicados a Apache Kafka dentro de la Plataforma de Seguridad Transaccional Adaptativa (PSTA).

El objetivo es garantizar la confidencialidad, integridad y disponibilidad de los eventos que soportan los procesos de autenticación, evaluación de riesgo y decisiones transaccionales.

---

# Objetivos

La estrategia busca:

- Proteger el backbone de eventos.
- Evitar accesos no autorizados.
- Garantizar integridad de mensajes.
- Proteger información sensible.
- Mantener trazabilidad completa.

---

# Principios

## Zero Trust

Todo productor y consumidor debe autenticarse y autorizarse explícitamente.

---

## Least Privilege

Cada servicio accede únicamente a los tópicos necesarios.

---

## Encryption by Default

Toda comunicación con Kafka debe estar cifrada.

---

## Auditability

Toda interacción debe ser trazable.

---

# Autenticación

La autenticación entre aplicaciones y Kafka se realiza mediante:

```text
mTLS
```

Cada servicio posee:

```text
Certificado
Identidad Propia
```

---

# Autorización

El acceso a los tópicos se controla mediante ACLs.

---

## Producer ACLs

Permiten publicar únicamente en los tópicos autorizados.

Ejemplo:

```text
Risk Assessment Service

Produce:
- risk.assessment.completed
```

---

## Consumer ACLs

Permiten consumir únicamente los eventos requeridos.

Ejemplo:

```text
Transaction Decision Service

Consume:
- risk.assessment.completed
```

---

# Cifrado

## En Tránsito

Todas las conexiones utilizan:

```text
TLS 1.3
mTLS
```

---

## En Reposo

Los datos almacenados utilizan:

```text
AES-256
AWS KMS
```

---

# Protección de Tópicos

Los eventos deben clasificarse según su sensibilidad.

---

## Alta Sensibilidad

```text
authentication.events
risk.assessment.events
transaction.events
```

Controles:

```text
ACLs
Auditoría
Retención Controlada
```

---

## Media Sensibilidad

```text
notification.events
audit.events
```

---

# Retención

La retención debe alinearse con los requerimientos regulatorios y operativos.

Ejemplo:

| Tipo de Evento | Retención |
|---------------|-----------|
| Transacciones | 30 días |
| Riesgo | 30 días |
| Auditoría | 90 días |
| Notificaciones | 7 días |

---

# Auditoría

Registrar:

```text
Producer
Consumer
Topic
Timestamp
Resultado
```

---

# Protección Operacional

## Topic Isolation

Separar eventos por dominio.

```text
risk.*
auth.*
transaction.*
audit.*
```

---

## Dead Letter Topics

Mensajes no procesados deben enviarse a:

```text
DLQ Topics
```

Ejemplo:

```text
risk.assessment.dlq
```

---

## Cuotas

Limitar consumo excesivo mediante:

```text
Producer Quotas
Consumer Quotas
```

---

# Observabilidad

Monitorear:

```text
Consumer Lag
Broker Health
Failed Authentications
DLQ Volume
```

---

## Alertas

Condiciones críticas:

```text
Consumer Lag Elevado
Broker Unavailable
Authentication Failures
DLQ Growth
```

---

# Beneficios

```text
Protección de Eventos
Control de Accesos
Integridad de Información
Cumplimiento Regulatorio
Mayor Trazabilidad
```

---

# Relación con Otros Documentos

Complementa:

```text
encryption.md
mtls-strategy.md
service-authentication.md
zero-trust.md
```

---

# Conclusión

Kafka constituye el backbone de eventos de la plataforma y debe protegerse mediante autenticación mutua, cifrado, autorización granular y monitoreo continuo. Estos controles garantizan la seguridad y confiabilidad de los eventos que soportan la detección de fraude y la autenticación adaptativa.