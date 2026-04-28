---
name: baseline-model-developer
description: Protocolo para la creación de modelos de referencia (Baselines) y la orquestación de experimentos iniciales para el ciclo TDD de IA.
user-invocable: false
agent: ai-data-scientist
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🏗️ I. Definición del Baseline Tecnológico
El agente debe establecer el punto de partida mínimo:
1. **Modelos Heurísticos / Reglas de Negocio:** Implementar el "Dummy Classifier/Regressor" o reglas simples de la industria para justificar el uso de ML.
2. **Modelo "Vanilla":** Entrenamiento de un algoritmo estándar (ej: Regresión Logística o Árbol de Decisión simple) sin optimización de hiperparámetros.
3. **Métricas Base:** Registro oficial de las métricas obtenidas por el proceso actual del cliente (si existe).

## 📐 II. Orquestación de Experimentos Iniciales
1. **Registro en Experiment Tracker:** Configuración de MLflow/W&B para que cada ejecución sea reproducible.
2. **Fijación de Semillas (Seeds):** Garantizar que la aleatoriedad sea controlada (`random_state`) en todas las etapas.
3. **Check de "Green" Inicial:** Proveer el script que pase el primer test de rendimiento definido por el QA Engineer.

## 🚀 III. Informe de Benchmarking
1. **Comparativa Baseline vs. Proceso Actual:** Cuantificación de la ganancia inicial de la IA.
2. **Hoja de Ruta de Mejora:** Basado en el baseline, proponer qué áreas del modelo requieren mayor esfuerzo de refinamiento.

---

> **Check de Certificación de Baseline:**
> - [ ] ¿Se han fijado las semillas para garantizar la reproducibilidad de los resultados?
> - [ ] ¿El baseline supera significativamente a una predicción aleatoria o una regla estúpida?
> - [ ] ¿Se han registrado todos los artefactos (modelo, métricas, código) en el repositorio de experimentos?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
