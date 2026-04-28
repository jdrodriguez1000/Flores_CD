---
name: model-performance-benchmarker
description: Protocolo para la validación técnica del rendimiento del modelo frente a los KPIs de negocio y criterios de aceptación definidos en la Phase Discovery.
user-invocable: false
agent: ai-model-qa-validator
allowed-tools: [Read, Write, Python-Interpreter]
---

## 🏗️ I. Verificación de Criterios de Aceptación (Thresholds)
El agente debe certificar que el modelo cumple con las expectativas mínimas:
1. **Validación de Métricas Core:** Confirmar que Precision, Recall, F1-Score, RMSE o MAPE superan los umbrales definidos.
2. **Comparativa vs. Baseline:** Certificar que la ganancia técnica respecto al Baseline justifica su despliegue.
3. **Métricas de Estabilidad:** Evaluar consistencia en diferentes particiones (K-Fold).
4. **Documentación Obligatoria:** El Model Validation Report debe guardarse en `docs/Phase_modeling/Model_Validation.md`.

## 📐 II. Análisis de Segmentos (Slice Discovery)
1. **Identificación de Áreas Críticas:** No basta con el promedio global; se debe evaluar el rendimiento en segmentos clave (ej: por zona geográfica, por tipo de cliente).
2. **Alertas de Degradación Localizada:** Detectar si el modelo es excelente para la mayoría pero falla catastróficamente en un segmento minoritario pero crítico.

## 🚀 III. Certificación de "Aprobado para Producción"
1. **Informe de Benchmarking:** Documento que resume el cumplimiento de los KPIs.
2. **Sello de Calidad Predictiva:** Aval técnico necesario para que el MLOps promueva el modelo a Staging.

---

> **Check de Certificación de Benchmarking:**
> - [ ] ¿El modelo supera todos los umbrales mínimos de aceptación?
> - [ ] ¿Se ha analizado el rendimiento en slices críticos de los datos?
> - [ ] ¿El reporte incluye una comparativa detallada contra el modelo v-anterior (si existe)?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
