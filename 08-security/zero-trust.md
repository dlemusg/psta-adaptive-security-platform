# Zero Trust Strategy

## Propósito

Este documento describe la estrategia Zero Trust adoptada por la Plataforma de Seguridad Transaccional Adaptativa (PSTA).

El objetivo es proteger la plataforma asumiendo que ningún usuario, servicio o componente es confiable por defecto.

---

# Objetivos

La estrategia busca:

- Reducir la superficie de ataque.
- Limitar el movimiento lateral.
- Proteger servicios críticos.
- Garantizar trazabilidad.
- Aplicar controles de acceso granulares.

---

# Principios

## Never Trust, Always Verify

Toda solicitud debe ser validada antes de ser procesada.

```text
Usuario
Servicio
Dispositivo
API
```

---

## Least Privilege

Cada identidad recibe únicamente los permisos necesarios.

---

## Verify Explicitly

Toda decisión de acceso debe considerar:

```text
Identidad
Contexto
Permisos
Nivel de Riesgo
```

---

## Assume Breach

La arquitectura asume que un componente podría estar comprometido.

Los controles deben minimizar el impacto.

---

# Aplicación en la Plataforma

## Usuarios

Autenticación mediante:

```text
JWT
MFA
Adaptive Authentication
```

---

## Microservicios

Autenticación mediante:

```text
mTLS
JWT Service Tokens
```

---

## Infraestructura

Control de acceso mediante:

```text
IAM Roles
RBAC
IRSA
```

---

## Red

Segmentación utilizando:

```text
Network Policies
Security Groups
Private Subnets
```

---

# Controles Clave

## Identidad

Cada entidad posee una identidad verificable.

```text
Usuario
Servicio
Aplicación
```

---

## Autenticación

Toda comunicación debe autenticarse.

---

## Autorización

Los permisos se asignan bajo el principio:

```text
Least Privilege
```

---

## Auditoría

Toda acción relevante debe registrarse.

```text
Accesos
Decisiones
Cambios
Operaciones Administrativas
```

---

# Beneficios

```text
Menor Superficie de Ataque
Mayor Trazabilidad
Reducción de Movimiento Lateral
Mejor Postura de Seguridad
```

---

# Relación con Otros Documentos

Complementa:

```text
service-authentication.md
mtls-strategy.md
secrets-management.md
kubernetes-hardening.md
```

---

# Conclusión

La estrategia Zero Trust establece la base de seguridad de la plataforma mediante la validación continua de identidades, la aplicación de mínimo privilegio y la segmentación de accesos. Este enfoque permite reducir riesgos y fortalecer la protección de los procesos críticos de autenticación, evaluación de riesgo y procesamiento transaccional.