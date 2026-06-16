# ADR-004: Motor de Riesgo Basado en Reglas como Estrategia Principal (Rules First)


## Contexto

La Plataforma de Seguridad Transaccional Adaptativa (PSTA) tiene como objetivo evaluar el riesgo de transacciones financieras en tiempo real para:

- Detectar fraude.
- Determinar autenticación adaptativa.
- Bloquear operaciones sospechosas.
- Proteger cuentas comprometidas.

El motor de riesgo es uno de los componentes más críticos de la solución, ya que influye directamente en:

```text
Aprobación de transacciones
Autenticación adicional
Bloqueo preventivo
Experiencia del cliente
Pérdidas por fraude
```

Durante el diseño surgió la decisión de definir cuál sería el mecanismo principal de evaluación de riesgo:

```text
Motor basado en reglas
Machine Learning
Modelo híbrido
```

---

## Problema

La evaluación de fraude requiere tomar decisiones en tiempo real utilizando múltiples señales:

```text
Monto
Beneficiario
Ubicación
Dispositivo
Velocidad transaccional
Patrones históricos
Comportamiento del cliente
```

La solución debe cumplir además con requisitos regulatorios y operativos:

- Explicabilidad.
- Auditabilidad.
- Trazabilidad.
- Baja latencia.
- Facilidad de ajuste por negocio.

La adopción exclusiva de Machine Learning presenta desafíos importantes.

---

## Alternativa 1: Machine Learning First

### Arquitectura

```text
Transaction
      ↓
ML Model
      ↓
Risk Score
```

---

### Ventajas

- Detecta patrones complejos.
- Identifica anomalías desconocidas.
- Aprende del comportamiento histórico.

---

### Desventajas

#### Baja Explicabilidad

Difícil responder:

```text
¿Por qué se rechazó esta transacción?
```

---

#### Dependencia de Datos

Requiere:

```text
Histórico de fraude
Entrenamiento
Etiquetado
Validación
```

---

#### Complejidad Operacional

Implica:

```text
MLOps
Feature Store
Model Registry
Model Monitoring
```

---

#### Riesgo Regulatorio

Los modelos pueden ser difíciles de justificar ante auditorías.

---

## Alternativa 2: Reglas de Negocio

### Arquitectura

```text
Transaction
      ↓
Fraud Rules
      ↓
Risk Score
```

---

### Ventajas

#### Explicabilidad

Cada decisión puede justificarse.

Ejemplo:

```text
Dispositivo nuevo = +30 puntos

Beneficiario nuevo = +25 puntos

Monto alto = +20 puntos
```

---

#### Auditabilidad

La razón de cada decisión queda registrada.

---

#### Facilidad de Ajuste

Las reglas pueden modificarse rápidamente.

---

#### Menor Complejidad

No requiere infraestructura de Machine Learning.

---

### Desventajas

#### Limitaciones Predictivas

Las reglas detectan únicamente escenarios conocidos.

---

#### Mantenimiento

El número de reglas puede crecer significativamente.

---

## Decisión

Se adopta una estrategia:

```text
Rules First
```

donde el motor principal de riesgo estará basado en reglas explícitas de negocio.

Los modelos predictivos serán incorporados posteriormente como complemento y no como mecanismo principal de decisión.

---

## Justificación

### 1. Explicabilidad

En banca es fundamental explicar:

```text
Por qué se aprobó
Por qué se rechazó
Por qué se autenticó
```

Una regla puede justificarse fácilmente.

Ejemplo:

```text
Nuevo dispositivo
+
Beneficiario nuevo
+
Monto superior al promedio
```

---

### 2. Auditabilidad

Cada decisión puede reconstruirse posteriormente.

Ejemplo:

```json
{
  "rule": "NEW_DEVICE",
  "score": 30
}
```

---

### 3. Agilidad de Negocio

Las reglas pueden modificarse rápidamente frente a nuevos patrones de fraude.

---

### 4. Menor Riesgo de Implementación

La plataforma puede comenzar a operar sin requerir:

```text
Entrenamiento
Históricos masivos
MLOps
```

---

### 5. Compatibilidad Regulatoria

Las entidades financieras suelen exigir capacidad de explicación y trazabilidad de decisiones.

---

## Arquitectura Resultante

```text
Transaction
      ↓
Risk Assessment Service
      ↓
Rule Engine
      ↓
Risk Score Calculator
      ↓
Risk Level
```

---

## Reglas Iniciales

### Device Trust

```text
Dispositivo conocido
```

Resultado:

```text
-10 puntos
```

---

### New Device

```text
Dispositivo nuevo
```

Resultado:

```text
+30 puntos
```

---

### Velocity Fraud

```text
Más de N transacciones
en ventana de tiempo
```

Resultado:

```text
+40 puntos
```

---

### High Amount

```text
Monto superior al histórico
```

Resultado:

```text
+25 puntos
```

---

### New Beneficiary

```text
Beneficiario no utilizado anteriormente
```

Resultado:

```text
+20 puntos
```

---

### Geo Anomaly

```text
Ubicación inusual
```

Resultado:

```text
+35 puntos
```

---

## Cálculo de Riesgo

Ejemplo:

```text
Nuevo dispositivo       +30
Beneficiario nuevo      +20
Monto alto              +25

----------------------------
Risk Score              75
```

Resultado:

```text
HIGH
```

---

## Beneficios

### Transparencia

Cada decisión es explicable.

---

### Mantenibilidad

Las reglas pueden evolucionar independientemente.

---

### Velocidad de Implementación

Permite construir una primera versión funcional rápidamente.

---

### Seguridad

Las reglas críticas pueden implementarse inmediatamente.

---

## Consecuencias Positivas

### Time To Market

Menor tiempo de implementación.

---

### Menor Complejidad Operacional

No se requiere ecosistema MLOps.

---

### Facilidad de Auditoría

Compatible con procesos regulatorios.

---

### Menor Riesgo Técnico

Reduce incertidumbre inicial.

---

## Consecuencias Negativas

### Menor Capacidad Predictiva

No detecta patrones desconocidos.

---

### Crecimiento de Reglas

El número de reglas puede aumentar con el tiempo.

---

### Dependencia de Expertos

La calidad depende de la experiencia del equipo antifraude.

---

## Evolución Futura

La decisión no excluye Machine Learning.

La evolución prevista es:

```text
Rules Engine
       +
ML Engine
       ↓
Hybrid Risk Engine
```

---

## Roadmap

### Fase 1

```text
Rules Only
```

---

### Fase 2

```text
Rules
+
Behavior Analytics
```

---

### Fase 3

```text
Rules
+
Machine Learning
```

---

### Fase 4

```text
Hybrid Risk Engine
```

---

## Relación con Otros ADR

Esta decisión soporta:

- ADR-001: Event-Driven Architecture.
- ADR-002: Kafka as Backbone.
- ADR-003: WebFlux over MVC.
- ADR-007: Hybrid Risk Engine.

---

## Impacto en la Arquitectura

El componente:

```text
Risk Assessment Service
```

implementará inicialmente:

```text
Rule Engine
Risk Score Calculator
Decision Policies
```

permitiendo incorporar capacidades predictivas posteriormente sin modificar el flujo principal de negocio.

---

## Decisión Final

Se adopta una estrategia **Rules First** para el motor de riesgo.

Las reglas explícitas de negocio constituyen el mecanismo principal de evaluación debido a su explicabilidad, auditabilidad, simplicidad operativa y compatibilidad con requerimientos regulatorios bancarios.

Los modelos de Machine Learning serán incorporados progresivamente como complemento dentro de una estrategia híbrida definida en el ADR-007.