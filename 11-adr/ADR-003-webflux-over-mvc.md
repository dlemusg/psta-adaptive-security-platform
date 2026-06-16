# ADR-003: Adopción de Spring WebFlux sobre Spring MVC


## Contexto

La Plataforma de Seguridad Transaccional Adaptativa (PSTA) debe procesar transacciones financieras en tiempo real, evaluar riesgo continuamente y reaccionar ante eventos de fraude con baja latencia.

Los servicios principales de la plataforma son:

```text
Risk Assessment Service
Adaptive Authentication Service
Transaction Decision Service
Account Protection Service
```

Estos servicios operan bajo una arquitectura:

```text
Microservices
+
Event-Driven Architecture
+
Kafka
+
Kubernetes
```

Además, la solución debe soportar:

- Miles de solicitudes concurrentes.
- Procesamiento de eventos en tiempo real.
- Integración intensiva con Kafka.
- Baja latencia.
- Escalabilidad horizontal.
- Uso eficiente de recursos.

---

## Problema

El modelo tradicional basado en Spring MVC utiliza una arquitectura bloqueante (Thread Per Request).

Para cada solicitud recibida:

```text
Request
   ↓
Thread
   ↓
Response
```

Durante operaciones I/O el hilo permanece ocupado.

Ejemplo:

```text
Request
   ↓
Consulta BD
   ↓
Esperar respuesta
   ↓
Continuar procesamiento
```

En escenarios de alta concurrencia esto genera:

- Alto consumo de memoria.
- Saturación de hilos.
- Incremento de latencia.
- Menor capacidad de escalamiento.

---

## Decisión

Se adopta Spring WebFlux como framework principal para el desarrollo de los microservicios de la plataforma.

La implementación se basará en:

```text
Spring Boot
+
Spring WebFlux
+
Project Reactor
```

---

## Principios Adoptados

### Programación Reactiva

La aplicación se construye sobre flujos asíncronos y no bloqueantes.

---

### Event-Driven Processing

Los servicios reaccionan a eventos y señales.

---

### Backpressure

Los consumidores pueden regular la velocidad de procesamiento.

---

### Resource Efficiency

La plataforma optimiza el uso de CPU y memoria.

---

## Arquitectura Tradicional (MVC)

```text
Request 1 → Thread 1

Request 2 → Thread 2

Request 3 → Thread 3

Request N → Thread N
```

Cada solicitud consume un hilo dedicado.

---

## Arquitectura Reactiva (WebFlux)

```text
Request 1
Request 2
Request 3
Request N

       ↓

Event Loop

       ↓

Non Blocking Processing
```

Un conjunto reducido de hilos puede procesar miles de solicitudes concurrentes.

---

# Justificación Técnica

## 1. Integración Natural con Kafka

La plataforma se fundamenta en eventos.

Kafka ya opera bajo un paradigma asíncrono.

```text
Producer
    ↓
Kafka
    ↓
Consumer
```

WebFlux se integra naturalmente con este modelo.

---

## 2. Mayor Concurrencia

Spring MVC:

```text
1000 requests
≈ 1000 threads
```

---

Spring WebFlux:

```text
1000 requests
≈ pocas decenas de threads
```

---

## 3. Menor Consumo de Recursos

Beneficios:

```text
Menor uso de memoria
Menor contexto de switching
Mayor densidad por nodo
```

---

## 4. Mejor Escalabilidad

Permite soportar:

```text
Picos transaccionales
Eventos masivos
Procesamiento concurrente
```

sin incrementar proporcionalmente los recursos.

---

## 5. Baja Latencia

La evaluación de riesgo debe completarse en milisegundos.

La programación reactiva reduce tiempos asociados al bloqueo de hilos.

---

## Ejemplo de Programación

### MVC

```java
RiskAssessment risk = repository.findById(id);

return calculateRisk(risk);
```

---

### WebFlux

```java
return repository.findById(id)
        .map(this::calculateRisk);
```

---

## Beneficios

### Escalabilidad Horizontal

Cada pod puede procesar más solicitudes concurrentes.

---

### Menor Consumo de Infraestructura

Se requieren menos recursos para la misma carga.

---

### Mejor Integración con Kafka

El modelo reactivo es consistente con la arquitectura orientada a eventos.

---

### Backpressure

Evita sobrecargar consumidores.

---

### Mejor Rendimiento Bajo Carga

Particularmente en operaciones I/O intensivas.

---

## Consecuencias Positivas

### Arquitectura Consistente

Toda la plataforma utiliza un modelo reactivo.

---

### Mayor Throughput

Permite procesar más transacciones por segundo.

---

### Menor Latencia

Reduce tiempos de espera asociados a bloqueos.

---

### Preparación para Escenarios de Alta Escala

Compatible con crecimiento futuro de volumen transaccional.

---

## Consecuencias Negativas

### Curva de Aprendizaje

Los desarrolladores deben familiarizarse con:

```text
Mono
Flux
Project Reactor
Backpressure
```

---

### Mayor Complejidad de Debugging

Los flujos reactivos son más complejos de analizar que el código imperativo.

---

### Integraciones Bloqueantes

Algunas librerías tradicionales siguen siendo bloqueantes.

Ejemplos:

```text
JDBC
SDKs Legacy
Drivers antiguos
```

---

### Necesidad de Disciplina Arquitectónica

Debe evitarse:

```java
.block()
```

en flujos reactivos.

---

## Relación con el Scaffold Bancolombia

El Scaffold Bancolombia soporta:

```text
Spring WebFlux
Clean Architecture
Reactive Programming
```

por lo que la decisión es consistente con la arquitectura propuesta.

---

## Casos de Uso Beneficiados

### Risk Assessment Service

Procesamiento concurrente de múltiples señales de riesgo.

---

### Adaptive Authentication Service

Gestión simultánea de autenticaciones.

---

### Transaction Decision Service

Evaluación concurrente de decisiones transaccionales.

---

### Account Protection Service

Procesamiento masivo de eventos de fraude.

---

## Alternativas Evaluadas

### Spring MVC

#### Ventajas

- Menor curva de aprendizaje.
- Amplia adopción.

#### Desventajas

- Arquitectura bloqueante.
- Menor escalabilidad.
- Menor eficiencia bajo alta concurrencia.

**Descartada.**

---

### Vert.x

#### Ventajas

- Alto rendimiento.
- Modelo reactivo.

#### Desventajas

- Menor adopción corporativa.
- Menor alineación con el ecosistema Spring.

**Descartada.**

---

### Quarkus

#### Ventajas

- Bajo consumo de memoria.
- Excelente rendimiento.

#### Desventajas

- Menor alineación con estándares internos.
- Menor experiencia organizacional.

**Descartada.**

---

## Relación con Otros ADR

Esta decisión soporta:

- ADR-001: Event-Driven Architecture.
- ADR-002: Kafka as Backbone.
- ADR-006: Kubernetes.
- ADR-007: Hybrid Risk Engine.

---

## Impacto en la Arquitectura

Todos los microservicios serán implementados utilizando:

```text
Spring Boot
Spring WebFlux
Project Reactor
```

La comunicación síncrona utilizará:

```text
Reactive REST APIs
```

La comunicación asíncrona utilizará:

```text
Kafka
```

manteniendo un modelo de programación consistente en toda la plataforma.

---

## Decisión Final

Se adopta Spring WebFlux sobre Spring MVC debido a su capacidad para soportar programación reactiva, procesamiento no bloqueante, alta concurrencia y mejor integración con una arquitectura orientada a eventos basada en Kafka.

La decisión permite construir una plataforma más escalable, eficiente y preparada para procesar grandes volúmenes de eventos financieros en tiempo real, alineándose además con las prácticas promovidas por el Scaffold Bancolombia y los principios de arquitectura reactiva definidos para la solución.