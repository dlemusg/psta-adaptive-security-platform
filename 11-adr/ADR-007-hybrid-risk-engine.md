# ADR-007: Adopción de un Motor de Riesgo Híbrido (Rules + Machine Learning)


## Contexto

El ADR-004 definió que la primera versión de la Plataforma de Seguridad Transaccional Adaptativa (PSTA) utilizaría una estrategia:

```text
Rules First
```

basada en reglas explícitas de negocio para garantizar:

- Explicabilidad.
- Auditabilidad.
- Trazabilidad.
- Rapidez de implementación.

Sin embargo, durante el análisis de evolución de la plataforma se identificó que los motores exclusivamente basados en reglas presentan limitaciones para detectar:

```text
Fraude emergente
Patrones desconocidos
Anomalías complejas
Ataques sofisticados
```

A medida que el volumen transaccional crece, la organización puede aprovechar los eventos históricos almacenados en Kafka para construir capacidades predictivas avanzadas.

---

## Problema

Existen dos enfoques principales para calcular el riesgo.

### Reglas de Negocio

```text
Transaction
      ↓
Rules Engine
      ↓
Risk Score
```

---

### Machine Learning

```text
Transaction
      ↓
ML Model
      ↓
Risk Score
```

---

Ambos enfoques presentan ventajas y limitaciones.

La plataforma necesita aprovechar las fortalezas de ambos sin sacrificar:

```text
Explicabilidad
Auditabilidad
Resiliencia
Gobernabilidad
```

---

## Alternativas Evaluadas

### Alternativa 1: Rules Only

---

#### Ventajas

```text
Explicabilidad
Auditabilidad
Facilidad de ajuste
```

---

#### Desventajas

```text
No detecta patrones desconocidos
Difícil adaptación automática
```

---

### Resultado

Adecuada para la fase inicial, pero insuficiente a largo plazo.

---

### Alternativa 2: Machine Learning Only

---

#### Ventajas

```text
Detección avanzada
Capacidad predictiva
Aprendizaje continuo
```

---

#### Desventajas

```text
Menor explicabilidad
Mayor complejidad operativa
Dependencia de datos históricos
```

---

### Resultado

No adecuada como mecanismo único de decisión para un entorno bancario.

---

### Alternativa 3: Motor Híbrido

---

#### Arquitectura

```text
Rules Engine
      +
ML Engine
      ↓
Risk Fusion Layer
      ↓
Final Risk Score
```

---

### Resultado

**Seleccionada**

---

## Decisión

Se adopta una arquitectura híbrida para la evolución del motor de riesgo.

La plataforma combinará:

```text
Motor de Reglas
+
Motor Predictivo
```

para producir una evaluación de riesgo consolidada.

---

# Principios Adoptados

## Rules Remain the Source of Truth

Las reglas continúan siendo el mecanismo principal de control.

---

## ML as Risk Amplifier

El Machine Learning complementa la evaluación.

No reemplaza completamente las reglas.

---

## Explainability First

Toda decisión debe poder justificarse.

---

## Gradual Adoption

Las capacidades predictivas se incorporarán progresivamente.

---

## Event Driven Learning

Los modelos se alimentan utilizando eventos históricos de Kafka.

---

# Arquitectura Resultante

```text
Transaction
      │
      ▼

+------------------+
| Rules Engine     |
+------------------+

      │
      ▼

Rule Score

      │

      ▼

+------------------+
| ML Engine        |
+------------------+

      │
      ▼

ML Score

      │
      ▼

+------------------+
| Risk Fusion      |
+------------------+

      │
      ▼

Final Risk Score
```

---

# Componentes

## Rules Engine

Evalúa reglas explícitas.

Ejemplos:

```text
New Device
Velocity Fraud
New Beneficiary
Geo Anomaly
High Amount
```

---

## ML Engine

Evalúa patrones complejos.

Ejemplos:

```text
Behavioral Anomalies
Session Anomalies
Fraud Clusters
Risk Prediction
```

---

## Risk Fusion Layer

Responsable de combinar ambos resultados.

---

## Risk Score Calculator

Calcula el riesgo final.

---

# Estrategia de Evolución

## Fase 1

### Rules Only

```text
Rules Engine
```

---

## Fase 2

### Shadow Mode

```text
Rules Engine
+
ML Engine
```

El ML observa y genera score, pero no participa en decisiones.

---

## Fase 3

### Assisted Decision

```text
Rules Engine
+
ML Recommendation
```

El score ML complementa las reglas.

---

## Fase 4

### Full Hybrid

```text
Rules Engine
+
ML Engine
+
Risk Fusion
```

---

# Ejemplo de Evaluación

## Rules Engine

```text
Nuevo Dispositivo      +30
Beneficiario Nuevo     +20
Monto Alto             +25
```

Resultado:

```text
Rule Score = 75
```

---

## ML Engine

Analiza:

```text
Histórico
Comportamiento
Sesión
Patrones
```

Resultado:

```text
ML Score = 0.91
```

---

## Risk Fusion

Combina:

```text
Rule Score = 75

ML Score = 0.91
```

Resultado:

```text
Final Risk Score = 92
Risk Level = CRITICAL
```

---

# Estrategias de Fusión

## Weighted Average

```text
70% Rules
30% ML
```

---

## Maximum Risk

```text
Max(Rules, ML)
```

---

## Risk Escalation

```text
ML sólo puede aumentar riesgo
```

---

## Estrategia Seleccionada

Para la plataforma se adopta:

```text
Risk Escalation
```

---

### Justificación

Nunca se permite que el ML reduzca una decisión tomada por las reglas.

Ejemplo:

```text
Rules = HIGH

ML = LOW
```

Resultado:

```text
HIGH
```

---

# Beneficios

## Explicabilidad

Las reglas siguen siendo visibles.

---

## Detección Avanzada

El ML identifica patrones desconocidos.

---

## Evolución Gradual

Permite incorporar IA sin rediseñar la plataforma.

---

## Menor Riesgo Operativo

Las reglas mantienen el control principal.

---

## Reutilización de Eventos

Kafka se convierte en fuente natural para entrenamiento.

---

# Integración con Kafka

Los eventos publicados alimentan modelos analíticos.

Ejemplos:

```text
TransactionReceived
RiskScoreCalculated
AuthenticationCompleted
TransactionRejected
AccountBlocked
```

---

## Beneficios

```text
Feature Engineering
Model Training
Model Monitoring
```

---

# Capacidades Futuras

## Behavioral Profiling

Modelado del comportamiento normal del cliente.

---

## Anomaly Detection

Identificación automática de patrones anómalos.

---

## Fraud Clustering

Agrupación de comportamientos sospechosos.

---

## Risk Prediction

Predicción temprana de fraude.

---

# Consecuencias Positivas

### Mayor Precisión

Mejora la detección de fraude.

---

### Menor Falso Negativo

Reduce fraudes no detectados.

---

### Evolución Tecnológica

Prepara la plataforma para capacidades avanzadas.

---

### Escalabilidad Analítica

Permite incorporar nuevos modelos sin modificar servicios.

---

# Consecuencias Negativas

### Mayor Complejidad

Aparecen nuevos componentes.

---

### MLOps

Requiere:

```text
Model Registry
Monitoring
Retraining
Validation
```

---

### Gobernanza

Los modelos deben ser auditables.

---

### Costos Operativos

Incremento de infraestructura analítica.

---

# Arquitectura Objetivo

```text
Kafka
  │
  ▼

Feature Engineering

  │
  ▼

Model Training

  │
  ▼

Model Registry

  │
  ▼

ML Inference Service

  │
  ▼

Risk Assessment Service
```

---

# Relación con Otros ADR

Esta decisión extiende:

- ADR-001: Event-Driven Architecture.
- ADR-002: Kafka as Backbone.
- ADR-003: WebFlux over MVC.
- ADR-004: Risk Engine Rules First.
- ADR-006: Kubernetes.

---

# Impacto en la Arquitectura

La primera versión de la plataforma operará utilizando exclusivamente reglas de negocio.

La arquitectura, sin embargo, queda preparada para incorporar:

```text
Behavior Analytics
Machine Learning
Fraud Prediction
Anomaly Detection
```

sin modificar los contratos ni el flujo principal de procesamiento.

---

# Decisión Final

Se adopta una estrategia de evolución hacia un motor de riesgo híbrido compuesto por reglas de negocio y capacidades de Machine Learning.

Las reglas continúan siendo la fuente principal de decisión debido a requisitos de explicabilidad y auditoría, mientras que los modelos predictivos actuarán como amplificadores de riesgo que permitirán mejorar progresivamente la capacidad de detección de fraude sin comprometer la gobernabilidad de la plataforma.