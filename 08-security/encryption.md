# Encryption Strategy

## Propósito

Este documento describe la estrategia de cifrado para la Plataforma de Seguridad Transaccional Adaptativa (PSTA).

El objetivo es proteger la confidencialidad, integridad y privacidad de la información durante todo su ciclo de vida, cumpliendo los requerimientos de seguridad y regulación del sector financiero.

---

# Objetivos

La estrategia busca:

- Proteger datos sensibles.
- Garantizar confidencialidad.
- Garantizar integridad.
- Reducir riesgo de exposición.
- Cumplir estándares regulatorios.

---

# Principios

## Encryption by Default

Toda información sensible debe estar cifrada.

---

## Defense in Depth

El cifrado debe aplicarse en múltiples capas.

```text
Datos en Tránsito
Datos en Reposo
Backups
Mensajería
```

---

## Gestión Centralizada de Claves

Las claves criptográficas deben administrarse de forma centralizada y auditada.

---

# Datos en Tránsito

Todas las comunicaciones utilizan:

```text
TLS 1.3
mTLS
HTTPS
```

---

## Aplicación

```text
Canales Digitales
APIs
Microservicios
Kafka
Bases de Datos
```

---

# Datos en Reposo

Toda persistencia debe utilizar cifrado nativo.

---

## PostgreSQL

```text
AWS RDS Encryption
AES-256
AWS KMS
```

---

## Redis

```text
Encryption at Rest
TLS Enabled
```

---

## Kafka

```text
Encrypted Storage
TLS Communication
```

---

## S3

```text
SSE-KMS
AES-256
```

---

## EBS Volumes

```text
AWS KMS
AES-256
```

---

# Gestión de Claves

## Herramienta

```text
AWS KMS
```

---

## Capacidades

```text
Key Rotation
Access Control
Auditing
Lifecycle Management
```

---

# Algoritmos Recomendados

## Simétricos

```text
AES-256
```

---

## Asimétricos

```text
RSA-2048+
ECC
```

---

## Hashing

```text
SHA-256
SHA-512
```

---

# Información Sensible

Debe protegerse especialmente:

```text
Credenciales
Tokens
OTP
Datos de Clientes
Información Transaccional
```

---

# Observabilidad

Toda operación criptográfica relevante debe ser auditada.

---

## Auditoría

```text
Key Usage
Key Rotation
Access Attempts
Decryption Events
```

---

# Beneficios

```text
Confidencialidad
Integridad
Cumplimiento Regulatorio
Protección de Datos
```

---

# Relación con Otros Documentos

Complementa:

```text
mtls-strategy.md
secrets-management.md
kafka-security.md
zero-trust.md
```

---

# Conclusión

La estrategia de cifrado garantiza la protección de la información en tránsito y en reposo mediante el uso de estándares modernos como TLS 1.3, AES-256 y AWS KMS. Su aplicación transversal fortalece la seguridad de la plataforma y reduce el riesgo de exposición de datos sensibles.