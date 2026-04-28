---
name: model-industrialization-specialist
description: Protocolo para la encapsulación de modelos de Machine Learning en clases de Python modulares y estandarizadas siguiendo el SpecDD.
user-invocable: false
agent: ai-ml-engineer
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🏗️ I. Encapsulamiento en Clases de Producción
El agente debe traducir el modelo experimental a una estructura de software robusta:
1. **Interfaz Estándar:** Implementación de clases que hereden de interfaces base, asegurando métodos `.fit()`, `.predict()` y `.predict_proba()` (si aplica) con firmas consistentes.
2. **Modularización de Componentes:** Separar la lógica de carga de datos, preprocesamiento e inferencia dentro de la clase del modelo.
3. **Validación de Tipos Técnica:** Uso obligatorio de *type hinting* para los parámetros de entrada (X) y salida (y) del modelo.

## 📐 II. Cumplimiento de SpecDD
1. **Configuración Externa:** Asegurar que los hiperparámetros se carguen desde archivos de configuración (`.yaml` / `.json`) y no estén hardcodeados.
2. **Inyección de Dependencias:** Diseñar la clase para que los escaladores y codificadores (Capa Gold) sean inyectados y no recalculados internamente.

## 🚀 III. Robustez de la Clase
1. **Manejo de Out-of-Distribution (OOD):** Implementar lógica para manejar categorías nuevas o valores fuera de rango no vistos en el entrenamiento.
2. **Documentación Técnica (Docstrings):** Especificar detalladamente la arquitectura del modelo, versión de la librería y dependencias necesarias.

---

> **Check de Certificación de Industrialización:**
> - [ ] ¿La clase del modelo sigue estrictamente la firma definida en el SpecDD?
> - [ ] ¿Se han desacoplado los parámetros del modelo de la lógica del código?
> - [ ] ¿El reporte de errores de la clase es informativo para el Backend Engineer?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
