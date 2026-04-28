---
name: algorithm-architecture-evaluator
description: Protocolo para la evaluación y selección de arquitecturas de Machine Learning (GBM, Deep Learning, Transformers) basadas en la naturaleza de los datos y objetivos de negocio.
user-invocable: false
agent: ai-data-scientist
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Evaluación de la Naturaleza de los Datos
Antes de seleccionar el algoritmo, el agente debe diagnosticar los datos "Gold":
1. **Linealidad vs. No-linealidad:** Determinar si relaciones simples capturan la señal o si se requiere alta expresividad.
2. **Cardinalidad y Estructura:** Evaluar si hay datos tabulares masivos (XGBoost/LightGBM) o secuencias/texto (Transformers/RNN).
3. **Restricciones de Negocio:** Considerar si se requiere alta interpretabilidad o si se prima la potencia predictiva (Caja Negra).

## 📐 II. Ranking de Arquitecturas Producibles
El agente debe contrastar al menos tres arquitecturas candidatas:
1. **Modelos Lineales/Basados en Árboles:** (Random Forest, CatBoost, LightGBM) para datos tabulares.
2. **Deep Learning / Redes Neuronales:** Para datos con jerarquías complejas o alta dimensionalidad.
3. **Transfer Learning:** Evaluación de modelos pre-entrenados si el dominio lo permite (ej: embeddings de texto/imagen).

## 🚀 III. Matriz de Decisión Algorítmica
El agente debe documentar la elección final basándose en:
1. **Métricas Técnicas de Entrenamiento.**
2. **Tiempo de Inferencia Estimado (SLA del Architect).**
3. **Complejidad de Mantenimiento.**

---

> **Check de Certificación de Algoritmo:**
> - [ ] ¿Se ha justificado la elección del algoritmo frente a alternativas más simples?
> - [ ] ¿La arquitectura seleccionada es compatible con el stack de serialización definido en el SAD?
> - [ ] ¿Se ha evaluado la robustez del modelo ante ruidos pequeños en los datos de entrada?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
