# ADR-005: Uso de mTLS y JWT para Seguridad entre Servicios

## Contexto

La Plataforma de Seguridad Transaccional Adaptativa (PSTA) opera sobre una arquitectura distribuida compuesta por múltiples microservicios desplegados en Kubernetes.

Los principales servicios incluyen:

```text
Risk Assessment Service
Adaptive Authentication Service
Transaction Decision Service
Account Protection Service
Customer Profile Service
Device Trust Service
Audit Service
```

Estos componentes intercambian información sensible relacionada con:

```text
Clientes
Transacciones
Autenticaciones
Riesgo
Fraude
```

La solución debe garantizar:

- Confidencialidad.
- Integridad.
- Autenticación.
- Autorización.
- No repudio.
- Trazabilidad.

Además, debe alinearse con prácticas de seguridad utilizadas en plataformas financieras y bancarias.

---

## Problema

Existen dos tipos de comunicación dentro de la solución:

### Comunicación Externa

```text
Canal Digital
        ↓
API Gateway
        ↓
Microservicios
```

---

### Comunicación Interna

```text
Microservicio
        ↓
Microservicio
```

Cada tipo de comunicación requiere mecanismos de seguridad diferentes.

La plataforma debe responder:

```text
¿Quién es el usuario?

¿Quién es el servicio?

¿Está autorizado?

¿Se puede confiar en el origen?
```

---

## Alternativas Evaluadas

### Alternativa 1: JWT Únicamente

```text
Cliente
    ↓
JWT
    ↓
Todos los Servicios
```

---

### Ventajas

- Fácil implementación.
- Amplia adopción.
- Compatible con OAuth2/OpenID Connect.

---

### Desventajas

No autentica el servicio origen.

Ejemplo:

```text
Risk Service
```

no puede validar realmente que la llamada proviene de:

```text
Transaction Decision Service
```

---

### Riesgo

Un servicio comprometido podría intentar suplantar otro servicio.

---

## Alternativa 2: mTLS Únicamente

```text
Servicio A
      ↔
Servicio B
```

mediante certificados mutuos.

---

### Ventajas

- Autenticación fuerte.
- Confianza mutua.
- Protección contra spoofing.

---

### Desventajas

No transporta contexto de usuario.

Ejemplo:

```text
¿Quién inició la transacción?
```

La información no viaja en el certificado.

---

## Alternativa 3: mTLS + JWT

```text
Usuario
      ↓
JWT

Servicio
      ↔
mTLS
      ↔
Servicio
```

---

### Resultado

Permite separar:

```text
Identidad del Usuario
```

de

```text
Identidad del Servicio
```

---

## Decisión

Se adopta una estrategia de seguridad híbrida basada en:

```text
JWT
+
mTLS
```

---

## Principios Adoptados

### JWT para Identidad del Usuario

Permite propagar el contexto del cliente autenticado.

---

### mTLS para Identidad del Servicio

Permite validar la identidad de los microservicios.

---

### Zero Trust

Ninguna comunicación interna es considerada confiable por defecto.

---

### Defense in Depth

La seguridad se implementa mediante múltiples capas complementarias.

---

# Arquitectura Resultante

## Tráfico Externo

```text
Cliente
    ↓
OAuth2/OpenID Connect
    ↓
JWT
    ↓
API Gateway
    ↓
Microservicios
```

---

## Tráfico Interno

```text
Transaction Decision Service
           ↔
          mTLS
           ↔
Risk Assessment Service
```

---

## Modelo Completo

```text
Usuario
   ↓
JWT

API Gateway

   ↓

Transaction Decision Service

   ↔ mTLS ↔

Risk Assessment Service

   ↔ mTLS ↔

Adaptive Authentication Service

   ↔ mTLS ↔

Account Protection Service
```

---

# JWT

## Objetivo

Representar la identidad autenticada del cliente.

---

## Contenido

Ejemplo:

```json
{
  "sub": "customer-123",
  "sessionId": "ABC123",
  "deviceId": "DEVICE-01",
  "roles": ["CUSTOMER"],
  "iat": 1710000000,
  "exp": 1710003600
}
```

---

## Beneficios

### Stateless

No requiere sesiones centralizadas.

---

### Escalabilidad

Compatible con arquitecturas distribuidas.

---

### Integración

Compatible con:

```text
OAuth2
OpenID Connect
Azure AD
Cognito
```

---

# mTLS

## Objetivo

Garantizar autenticación mutua entre servicios.

---

## Funcionamiento

### TLS Tradicional

```text
Cliente valida Servidor
```

---

### mTLS

```text
Cliente valida Servidor

Servidor valida Cliente
```

---

## Beneficios

### Identidad Fuerte

Cada servicio posee un certificado propio.

---

### Prevención de Suplantación

Un servicio no puede hacerse pasar por otro.

---

### Protección de Tráfico Interno

Todo el tráfico interno viaja cifrado.

---

### Zero Trust

No se confía implícitamente en la red.

---

# Justificación Técnica

## 1. Separación de Identidades

JWT representa:

```text
Usuario
```

mTLS representa:

```text
Servicio
```

---

## 2. Cumplimiento de Buenas Prácticas

Las plataformas bancarias modernas utilizan:

```text
OAuth2
OIDC
JWT
mTLS
```

como estándar de seguridad.

---

## 3. Compatibilidad con Kubernetes

Service Meshes modernos soportan:

```text
Istio
Linkerd
AWS App Mesh
```

---

## 4. Trazabilidad

Permite registrar:

```text
Usuario
Servicio
Transacción
```

en cada operación.

---

# Implementación

## Autenticación Externa

### Proveedor de Identidad

```text
Azure AD
```

o

```text
AWS Cognito
```

---

### Flujo

```text
Cliente
   ↓
Identity Provider
   ↓
JWT
   ↓
API Gateway
```

---

## Autenticación Interna

### Certificados

Emitidos por:

```text
Private CA
```

o

```text
AWS ACM Private CA
```

---

### Flujo

```text
Service A
      ↔
     mTLS
      ↔
Service B
```

---

# Gestión de Certificados

## Requisitos

- Rotación automática.
- Renovación automática.
- Revocación controlada.

---

## Solución Recomendada

```text
cert-manager
+
AWS ACM Private CA
```

---

# Beneficios

## Confidencialidad

Todo el tráfico viaja cifrado.

---

## Integridad

Se evita manipulación de mensajes.

---

## Autenticación

Usuarios y servicios son autenticados.

---

## No Repudio

Las acciones pueden asociarse a identidades específicas.

---

## Trazabilidad

Todas las llamadas pueden ser auditadas.

---

# Consecuencias Positivas

### Seguridad Bancaria

Alineada con prácticas empresariales modernas.

---

### Compatibilidad Cloud Native

Compatible con Kubernetes y Service Mesh.

---

### Escalabilidad

No depende de sesiones compartidas.

---

### Resiliencia

La autenticación distribuida elimina puntos únicos de falla.

---

# Consecuencias Negativas

### Complejidad Operacional

Se requiere gestión de certificados.

---

### Rotación de Certificados

Debe automatizarse.

---

### Curva de Aprendizaje

Los equipos deben comprender:

```text
PKI
TLS
mTLS
JWT
OAuth2
```

---

# Alternativas Descartadas

## API Keys

### Motivo

Seguridad insuficiente para servicios críticos.

---

## Basic Authentication

### Motivo

No cumple estándares modernos.

---

## Sesiones Compartidas

### Motivo

Introduce acoplamiento y problemas de escalabilidad.

---

# Relación con Otros ADR

Esta decisión complementa:

- ADR-001: Event-Driven Architecture.
- ADR-002: Kafka as Backbone.
- ADR-003: WebFlux over MVC.
- ADR-006: Kubernetes.
- ADR-007: Hybrid Risk Engine.

---

# Impacto en la Arquitectura

Todos los microservicios deberán:

### Validar JWT

Para obtener contexto de usuario.

---

### Utilizar mTLS

Para comunicación interna.

---

### Registrar Identidades

En logs y eventos de auditoría.

Ejemplo:

```text
customerId
serviceId
traceId
correlationId
```

---

# Decisión Final

Se adopta una estrategia híbrida basada en JWT y mTLS.

JWT será utilizado para representar la identidad y contexto del usuario autenticado, mientras que mTLS garantizará autenticación mutua y cifrado en todas las comunicaciones entre microservicios.

Esta combinación proporciona un modelo de seguridad alineado con principios Zero Trust, requisitos regulatorios bancarios y arquitecturas modernas basadas en Kubernetes y microservicios.