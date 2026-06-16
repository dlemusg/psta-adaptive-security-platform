# Service Authentication

## Propósito

Este documento describe la estrategia de autenticación entre servicios de la Plataforma de Seguridad Transaccional Adaptativa (PSTA).

El objetivo es garantizar que únicamente servicios autorizados puedan consumir APIs y eventos dentro de la plataforma.

---

# Objetivos

La estrategia busca:

- Verificar la identidad de cada servicio.
- Evitar suplantación de componentes.
- Proteger APIs internas.
- Garantizar trazabilidad.
- Implementar principios Zero Trust.

---

# Principios

## Authenticate Every Request

Toda comunicación entre servicios debe autenticarse.

---

## Zero Trust

Ningún servicio es confiable por defecto.

---

## Defense in Depth

La autenticación se realiza en múltiples capas.

```text
mTLS
+
JWT Service Tokens
```

---

# Estrategia

## Capa 1 — mTLS

Proporciona:

```text
Autenticación Mutua
Canal Cifrado
Verificación de Identidad
```

Valida que el servicio origen sea legítimo.

---

## Capa 2 — JWT Service Tokens

Cada servicio debe incluir un token firmado.

Validaciones:

```text
Issuer
Audience
Expiration
Scopes
```

---

# Flujo

```text
Service A
    ↓
mTLS Validation
    ↓
JWT Validation
    ↓
Service B
```

---

# Identidad de Servicio

Cada microservicio posee una identidad única.

Ejemplos:

```text
risk-assessment-service
adaptive-auth-service
transaction-decision-service
account-protection-service
audit-service
```

---

# Autorización

Los permisos se asignan mediante scopes.

Ejemplo:

```text
risk.read
risk.write
transaction.approve
transaction.reject
```

Cada servicio recibe únicamente los permisos requeridos.

---

# Integración con Kubernetes

La identidad de los servicios se asocia mediante:

```text
Service Accounts
IRSA
RBAC
```

---

# Integración con Kafka

Los productores y consumidores son autenticados mediante:

```text
mTLS
ACLs
```

---

# Observabilidad

Registrar:

```text
Service Identity
Issuer
Audience
Scopes
Authentication Result
```

---

# Beneficios

```text
Autenticación Mutua
Menor Riesgo de Suplantación
Trazabilidad
Cumplimiento Zero Trust
```

---

# Relación con Otros Documentos

Complementa:

```text
mtls-strategy.md
kafka-security.md
secrets-management.md
zero-trust.md
```

---

# Conclusión

La autenticación entre servicios se basa en una combinación de mTLS y JWT Service Tokens, permitiendo verificar identidades, proteger APIs internas y garantizar comunicaciones seguras dentro de la plataforma.