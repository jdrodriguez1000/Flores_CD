---
name: model-serialization-registry-manager
description: Protocolo para la serialización, versionado y registro oficial de modelos de IA en formatos estándar (ONNX, Pickle, Joblib).
user-invocable: false
agent: ai-ml-engineer
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Selección del Formato de Serialización
El agente debe elegir el formato basándose en el entorno de despliegue definido en el SAD:
1. **ONNX (Open Neural Network Exchange):** Prioritario para interoperabilidad y optimización en entornos de alta concurrencia.
2. **Joblib / Pickle:** Para modelos basados en Scikit-Learn donde el stack de inferencia es puramente Python.
3. **Formatos Nativos:** (H5 para Keras, PT para PyTorch) si el SAD lo requiere explícitamente.

## 📐 II. Protocolo de Registro y Versionado
1. **Model Registry (MLflow):** Registro oficial del artefacto con metadatos de:
   - Versión del modelo (Semantic Versioning).
   - Métricas de rendimiento finales.
   - Hash del dataset de entrenamiento.
2. **Tags de Estado:** Etiquetar el modelo como `Staging`, `Production` o `Archived`.

## 🚀 III. Empaquetamiento de Artefactos
1. **Bundle de Inferencia:** Guardar el modelo junto con sus objetos de preprocesamiento (Scalers, Encoders) para evitar discrepancias de datos.
2. **Validación de Carga:** Script de prueba que cargue el modelo serializado y verifique que la predicción coincida con el modelo en memoria.

---

> **Check de Certificación de Registro:**
> - [ ] ¿Se ha verificado que el archivo serializado puede cargarse en un entorno limpio sin dependencias de entrenamiento?
> - [ ] ¿El modelo está correctamente versionado en el registro oficial?
> - [ ] ¿Se han incluido los escaladores y codificadores necesarios para la inferencia completa?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
