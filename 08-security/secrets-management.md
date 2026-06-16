# Secrets Management

## Propósito

Este documento describe la estrategia de gestión de secretos para la Plataforma de Seguridad Transaccional Adaptativa (PSTA).

El objetivo es proteger credenciales, certificados, claves y tokens utilizados por los componentes de la plataforma.

---

# Objetivos

La estrategia busca:

- Centralizar la gestión de secretos.
- Evitar credenciales embebidas en código.
- Automatizar la rotación.
- Garantizar trazabilidad y auditoría.
- Aplicar el principio de mínimo privilegio.

---

# Principios

## No Secrets in Code

Está prohibido almacenar secretos en:

```text
Código Fuente
Repositorios Git
Archivos de Configuración
Imágenes Docker
```

---

## Centralized Management

Todos los secretos son gestionados desde una plataforma centralizada.

---

## Least Privilege

Cada servicio accede únicamente a los secretos que requiere.

---

## Automatic Rotation

Los secretos deben renovarse periódicamente.

---

# Solución

## AWS Secrets Manager

Repositorio principal para:

```text
Passwords
API Keys
JWT Secrets
Database Credentials
Certificates
```

---

## Kubernetes Integration

Los secretos son consumidos mediante:

```text
External Secrets Operator
```

Evita duplicar secretos dentro del clúster.

---

# Tipos de Secretos

## Aplicación

```text
JWT Keys
API Tokens
OAuth Credentials
```

---

## Infraestructura

```text
Database Credentials
Kafka Credentials
Redis Credentials
```

---

## Seguridad

```text
Certificates
Private Keys
mTLS Credentials
```

---

# Acceso

El acceso se controla mediante:

```text
IAM Roles
IRSA
RBAC
```

Cada servicio obtiene únicamente los secretos autorizados.

---

# Rotación

Frecuencia recomendada:

| Tipo | Rotación |
|--------|-----------|
| Passwords | 90 días |
| API Keys | 90 días |
| Certificados | 90 días |
| Tokens Críticos | Según proveedor |

---

# Auditoría

Registrar:

```text
Lectura
Actualización
Rotación
Intentos Fallidos
```

---

# Observabilidad

Monitorear:

```text
Secret Access
Secret Expiration
Rotation Status
```

---

# Beneficios

```text
Menor Riesgo de Exposición
Rotación Centralizada
Mayor Trazabilidad
Cumplimiento de Seguridad
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

La gestión centralizada de secretos mediante AWS Secrets Manager permite proteger credenciales críticas, reducir el riesgo de exposición y simplificar la operación de la plataforma mediante controles de acceso, auditoría y rotación automática.