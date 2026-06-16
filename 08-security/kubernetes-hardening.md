# Kubernetes Hardening

## Propósito

Este documento describe los controles de hardening aplicados al clúster Kubernetes que soporta la Plataforma de Seguridad Transaccional Adaptativa (PSTA).

El objetivo es reducir la superficie de ataque y proteger los servicios críticos de fraude, autenticación y procesamiento transaccional.

---

# Objetivos

La estrategia busca:

- Minimizar riesgos de compromiso.
- Proteger workloads y datos.
- Limitar accesos privilegiados.
- Aislar componentes críticos.
- Cumplir principios Zero Trust.

---

# Principios

## Least Privilege

Todos los accesos deben operar con los permisos mínimos necesarios.

---

## Defense in Depth

La seguridad debe aplicarse en múltiples capas.

```text
Cluster
Node
Network
Workload
Application
```

---

## Zero Trust

Ningún servicio o usuario es confiable por defecto.

---

# Seguridad de Workloads

## Contenedores Non-Root

Todos los contenedores deben ejecutarse como:

```text
Non-Root User
```

---

## Read-Only Filesystem

Siempre que sea posible:

```text
readOnlyRootFilesystem=true
```

---

## Security Context

Aplicar:

```text
runAsNonRoot
allowPrivilegeEscalation=false
```

---

# Seguridad de Red

## Network Policies

Restringir la comunicación entre namespaces y servicios.

Ejemplo:

```text
Risk Service
   ↓
Decision Service

Permitido
```

```text
Risk Service
   ↓
Cualquier Servicio

Denegado
```

---

## Private Networking

Los nodos y servicios internos operan en:

```text
Private Subnets
```

---

## Ingress Control

Exposición únicamente mediante:

```text
API Gateway
Ingress Controller
```

---

# Control de Acceso

## RBAC

Control granular de permisos.

Roles separados para:

```text
Administradores
Operación
Desarrollo
CI/CD
```

---

## IAM Roles for Service Accounts

Los Pods utilizan identidades propias.

```text
IRSA
```

Evita:

```text
Credenciales Compartidas
```

---

# Seguridad de Imágenes

## Trusted Registries

Solo imágenes provenientes de registros autorizados.

---

## Image Scanning

Escaneo automático en CI/CD.

Validar:

```text
CVEs
Dependencias Vulnerables
Misconfigurations
```

---

## Immutable Images

Las imágenes desplegadas no deben modificarse en ejecución.

---

# Gestión de Secretos

Los secretos no deben almacenarse en:

```text
Código Fuente
Manifiestos Git
Variables Hardcoded
```

Uso de:

```text
AWS Secrets Manager
External Secrets Operator
```

---

# Observabilidad y Auditoría

## Audit Logs

Registrar:

```text
Accesos
Cambios de Configuración
Operaciones Administrativas
```

---

## Runtime Monitoring

Monitorear:

```text
Pods
Nodos
Eventos de Seguridad
```

---

# Beneficios

```text
Menor Superficie de Ataque
Mayor Aislamiento
Protección de Workloads
Cumplimiento de Seguridad
```

---

# Relación con Otros Documentos

Complementa:

```text
zero-trust.md
service-authentication.md
secrets-management.md
encryption.md
```

---

# Conclusión

El hardening de Kubernetes fortalece la seguridad de la plataforma mediante controles de acceso, aislamiento de red, protección de workloads y gestión segura de secretos. Estos controles reducen significativamente el riesgo operativo y mejoran la postura de seguridad de la solución.