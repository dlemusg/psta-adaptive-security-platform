# Escenarios Adicionales Potenciales

## Propósito

Los escenarios definidos inicialmente cubren los principales requerimientos funcionales del reto:

- Transferencias de bajo riesgo.
- Dispositivos nuevos.
- Fraude por velocidad.
- Indisponibilidad del proveedor OTP.
- Escalamiento dinámico del riesgo de sesión.

Sin embargo, una plataforma antifraude empresarial debe contemplar escenarios adicionales que permitan validar capacidades avanzadas de seguridad, resiliencia, continuidad operativa y evolución futura.

Los siguientes escenarios representan extensiones naturales de la arquitectura propuesta y podrían ser incorporados en fases posteriores del diseño o implementación.

---

# Escenario 6: Transferencia hacia Beneficiario Nuevo de Alto Riesgo

## Objetivo

Validar la capacidad de la plataforma para incrementar dinámicamente el riesgo cuando una transacción involucra un beneficiario nunca antes utilizado por el cliente.

## Situación

- Cliente autenticado.
- Dispositivo confiable.
- Beneficiario nuevo.
- Monto superior al promedio histórico.

## Comportamiento Esperado

- Incremento del Risk Score.
- Activación de autenticación adaptativa.
- Aprobación únicamente tras validar la identidad.

## Capacidades Validadas

- Beneficiary Risk Assessment.
- Adaptive Authentication.
- Fraud Rule Engine.

---

# Escenario 7: Detección de Cambio Geográfico Imposible

## Objetivo

Detectar comportamientos incompatibles con desplazamientos físicos razonables.

## Situación

```text
08:00 AM -> Medellín
08:15 AM -> Londres
```

## Comportamiento Esperado

- Detección de Impossible Travel.
- Incremento del riesgo de sesión.
- Solicitud de autenticación adicional.
- Posible bloqueo preventivo.

## Capacidades Validadas

- Geo Risk Engine.
- Session Risk Management.
- Adaptive Authentication.

---

# Escenario 8: Intento de Account Takeover

## Objetivo

Detectar intentos de apropiación de cuenta mediante correlación de múltiples señales de riesgo.

## Situación

- Dispositivo desconocido.
- Nueva dirección IP.
- Cambio reciente de contraseña.
- Beneficiario nuevo.
- Transferencia de alto valor.

## Comportamiento Esperado

- Riesgo crítico.
- Activación de mecanismos de mitigación.
- Bloqueo preventivo de la cuenta.

## Capacidades Validadas

- Correlación de señales.
- Risk Assessment.
- Account Protection.

---

# Escenario 9: Falla Total de Factores de Autenticación

## Objetivo

Validar la resiliencia del proceso de autenticación cuando múltiples proveedores presentan indisponibilidad simultánea.

## Situación

- OTP indisponible.
- Push Authentication indisponible.

## Comportamiento Esperado

- Activación de mecanismos alternativos.
- Biometría o autenticación presencial.
- Continuidad controlada del servicio.

## Capacidades Validadas

- Authentication Orchestrator.
- Fallback Engine.
- Circuit Breaker.

---

# Escenario 10: Múltiples Intentos de Autenticación Fallidos

## Objetivo

Proteger la cuenta ante intentos repetitivos de autenticación incorrecta.

## Situación

- OTP inválido.
- Push rechazado.
- Múltiples intentos consecutivos.

## Comportamiento Esperado

- Generación de AuthenticationFailed.
- Incremento del riesgo.
- Bloqueo temporal o definitivo.

## Capacidades Validadas

- Adaptive Authentication.
- Account Protection.
- Fraud Detection.

---

# Escenario 11: Indisponibilidad del Motor de Riesgo

## Objetivo

Validar el comportamiento de la plataforma cuando el servicio de evaluación de riesgo no está disponible.

## Situación

- Risk Assessment Service fuera de servicio.

## Alternativas de Negocio

### Fail Closed

```text
Rechazar la transacción
```

### Step-Up Authentication

```text
Solicitar autenticación reforzada
```

### Riesgo Conservador

```text
Asumir riesgo alto
```

## Capacidades Validadas

- Resiliencia.
- Continuidad Operativa.
- Patrones de Degradación Controlada.

---

# Escenario 12: Indisponibilidad de Kafka

## Objetivo

Validar la continuidad operativa ante fallas temporales del backbone de eventos.

## Situación

- Kafka no disponible.

## Comportamiento Esperado

- Persistencia local mediante Outbox Pattern.
- Reintentos automáticos.
- Publicación diferida cuando Kafka se recupere.

## Capacidades Validadas

- Outbox Pattern.
- Eventual Consistency.
- Resiliencia Operacional.

---

# Escenario 13: Reprocesamiento de Eventos Históricos

## Objetivo

Validar la capacidad de recalcular decisiones históricas utilizando nuevas reglas de fraude.

## Situación

- Incorporación de nuevas reglas antifraude.
- Necesidad de recalcular los últimos 30 días.

## Comportamiento Esperado

- Reinicio de offsets.
- Reprocesamiento controlado.
- Generación de nuevos indicadores.

## Capacidades Validadas

- Kafka Event Replay.
- CQRS.
- Auditabilidad.

---

# Escenario 14: Investigación de Fraude por Analista

## Objetivo

Permitir la reconstrucción completa de una operación sospechosa.

## Situación

- Se detecta un evento FraudPatternDetected.

## Comportamiento Esperado

- Consulta de evidencias.
- Visualización de eventos relacionados.
- Reconstrucción cronológica.

## Capacidades Validadas

- Audit Service.
- OpenSearch.
- CQRS Read Models.

---

# Escenario 15: Caída Total de la Región Principal

## Objetivo

Validar la estrategia de continuidad del negocio ante un desastre regional.

## Situación

- Región primaria fuera de servicio.

## Comportamiento Esperado

- Activación de failover.
- Redirección de tráfico.
- Continuidad desde región secundaria.

## Capacidades Validadas

- Multi-Region Deployment.
- Disaster Recovery.
- Alta Disponibilidad.

---

