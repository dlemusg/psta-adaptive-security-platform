# mTLS Strategy

## Propósito

Este documento describe la estrategia de Mutual TLS (mTLS) para la Plataforma de Seguridad Transaccional Adaptativa (PSTA).

El objetivo es garantizar autenticación mutua y cifrado de las comunicaciones entre componentes internos de la plataforma.

---

# Objetivos

La estrategia busca:

- Autenticar clientes y servidores.
- Proteger la comunicación interna.
- Evitar suplantación de servicios.
- Garantizar integridad de la información.
- Implementar principios Zero Trust.

---

# Principios

## Mutual Authentication

Tanto cliente como servidor deben presentar certificados válidos.

```text
Cliente Verifica Servidor
+
Servidor Verifica Cliente
```

---

## Encryption by Default

Toda comunicación interna debe estar cifrada.

---

## Zero Trust

Ningún servicio es considerado confiable por defecto.

---

# Alcance

mTLS se aplica a:

```text
Microservicios
Kafka
APIs Internas
Ingress Interno
Servicios Compartidos
```

---

# Flujo

```text
Service A
     ↓
Presenta Certificado
     ↓
Service B
     ↓
Valida Certificado
     ↓
Canal Seguro Establecido
```

---

# Gestión de Certificados

## Autoridad Certificadora

Los certificados son emitidos por una CA privada.

Opciones:

```text
AWS Private CA
cert-manager
```

---

## Rotación

La renovación debe ser automática.

```text
90 días
```

---

## Revocación

Los certificados comprometidos deben poder invalidarse de forma inmediata.

---

# Integración con Kubernetes

Los certificados son distribuidos automáticamente mediante:

```text
cert-manager
```

Cada servicio recibe:

```text
Certificado
Clave Privada
Cadena de Confianza
```

---

# Integración con Kafka

Las conexiones entre:

```text
Producers
Consumers
Brokers
```

utilizan:

```text
TLS 1.3
mTLS
```

---

# Beneficios

```text
Autenticación Mutua
Canales Cifrados
Prevención de Suplantación
Cumplimiento de Zero Trust
```

---

# Observabilidad

Monitorear:

```text
Certificados Próximos a Expirar
Errores de Handshake
Conexiones Rechazadas
```

---

# Relación con Otros Documentos

Complementa:

```text
service-authentication.md
kafka-security.md
zero-trust.md
encryption.md
```

---

# Conclusión

mTLS constituye el mecanismo principal de autenticación entre servicios de la plataforma, garantizando comunicaciones cifradas, identidades verificables y una implementación consistente de los principios Zero Trust.