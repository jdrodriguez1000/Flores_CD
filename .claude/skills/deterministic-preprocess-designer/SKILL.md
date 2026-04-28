---
name: deterministic-preprocess-designer
description: Protocolo para la implementación de escaladores y codificadores (Preprocessing) que garanticen la consistencia matemática entre entrenamiento e inferencia.
user-invocable: false
agent: ai-feature-store-architect
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🏗️ I. Codificación de Variables Categóricas (Encoding)
El agente debe seleccionar el método óptimo para convertir texto en números:
1. **One-Hot Encoding:** Para variables con baja cardinalidad (<10 categorías).
2. **Target / Mean Encoding:** Para alta cardinalidad, cuidando de no introducir leakage (usando validación cruzada).
3. **Ordinal Encoding:** Solo si existe una jerarquía clara (ej: Bajo, Medio, Alto).

## 📐 II. Escalado y Normalización (Scaling)
1. **StandardScaler (Z-score):** Para variables con distribución normal.
2. **MinMaxScaler / RobustScaler:** Para variables con outliers o distribuciones no normales.
3. **Aplicación Determinística:** Garantizar que los parámetros del escalador (media, desviación) se calculen solo en el set de entrenamiento y se apliquen identicamente al de prueba y producción.

## 🚀 III. Empaquetamiento de Preproceso (Pipelines)
1. **Diseño de Scikit-Learn Pipelines:** Encapsular todas las transformaciones en un objeto serializable.
2. **Gestión de Statefulness:** Asegurar que los estados de los codificadores se guarden junto con los metadatos de la variable.

---

> **Check de Certificación de Preproceso:**
> - [ ] ¿Se ha evitado el Data Leakage al calcular los parámetros de escalado fuera del set de entrenamiento?
> - [ ] ¿El método de encoding seleccionado es eficiente para la cardinalidad de la variable?
> - [ ] ¿El pipeline de preproceso es reproducible en el entorno de inferencia (Phase Delivery)?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
