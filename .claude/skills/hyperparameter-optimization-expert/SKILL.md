---
name: hyperparameter-optimization-expert
description: Protocolo para la ejecución de optimización de hiperparámetros avanzada utilizando técnicas Bayesianas u Optuna para maximizar el rendimiento del modelo.
user-invocable: false
agent: ai-data-scientist
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Definición del Espacio de Búsqueda
El agente debe establecer los rangos técnicos para cada hiperparámetro crítico:
1. **Selección de Parámetros:** Identificar cuáles influyen más en el sobreajuste (*Overfitting*) vs. capacidad de aprendizaje.
2. **Definición de Distribuciones:** Utilizar escalas logarítmicas para parámetros de magnitud (ej: *learning rate*) y lineales para contadores (ej: *max depth*).
3. **Optimización Multi-objetivo:** Si es necesario, optimizar tanto la métrica principal como la latencia o el tamaño del modelo.

## 📐 II. Ejecución con Estrategia de Poda (Pruning)
1. **Bayesian Optimization:** Uso de procesos gaussianos para explorar el espacio de forma inteligente reduciendo el número de iteraciones.
2. **Early Stopping / Pruning:** Implementar algoritmos (ej: Median Pruner) para detener experimentos que no prometen superar al mejor resultado actual.
3. **Cross-Validation Robusta:** Asegurar que cada combinación de hiperparámetros sea evaluada mediante validación cruzada para garantizar la estabilidad.

## 🚀 III. Certificación de Hiperparámetros
1. **Análisis de Sensibilidad:** Identificar qué parámetros fueron determinantes en el éxito.
2. **Exportación de Configuración:** Generar el archivo `.json` o `.yaml` con la configuración final para el ML Engineer.

---

> **Check de Certificación de Optimización:**
> - [ ] ¿Se ha evitado el sobreajuste mediante una validación cruzada adecuada?
> - [ ] ¿El espacio de búsqueda definido es lo suficientemente amplio para encontrar el óptimo global?
> - [ ] ¿Se han registrado todas las pruebas en el sistema de Experiment Tracking (MLflow)?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
