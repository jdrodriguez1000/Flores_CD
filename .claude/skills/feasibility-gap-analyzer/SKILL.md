---
name: feasibility-gap-analyzer
description: Protocolo para contrastar los requerimientos de negocio con la disponibilidad técnica de datos, identificando brechas críticas y proponiendo estrategias de mitigación.
user-invocable: false
agent: ai-data-auditor
allowed-tools: [Read, Write, Edit]
---

## 🏗️ I. Mapeo Requerimiento vs. Realidad

Este protocolo cruza la Phase Discovery de negocio con los hallazgos del Auditor:
1. **Cruze de Variables:** Por cada User Story definida por el Estratega, listar las variables necesarias para cumplirla.
2. **Identificación de Brechas (Gaps):** Clasificar las faltantes en:
   - **Brecha de Disponibilidad:** El dato no existe.
   - **Brecha de Calidad:** El dato existe pero no es confiable.
   - **Brecha de Accesibilidad:** El dato existe pero no se puede extraer por temas legales o técnicos.

## 📐 II. Estrategias de Mitigación

Para cada brecha detectada, el agente debe proponer una solución técnica:
* **Proxy Variables:** Si la variable exacta no existe, ¿hay alguna que pueda actuar como sustituto cercano?
* **Data Augmentation / Synthesis:** ¿Es posible generar datos sintéticos o usar fuentes externas (Open Data) para compensar?
* **Imputation Strategy:** Proponer si los nulos deben ser eliminados, imputados por media/mediana, o si requieren un modelo de predicción de nulos.
* **Pivot de Requerimiento:** Si la brecha es insalvable, proponer al Estratega ajustar el KPI (ej: de predicción diaria a predicción semanal).

## 🚀 III. Data Feasibility Report (DFR)

Documento final de la Phase Discovery que contiene:
1. **Veredicto de Viabilidad:** (Go / No-Go / Go with Risks).
2. **Plan de Adquisición:** Pasos necesarios para obtener los datos faltantes antes de la Phase Engineering.
3. **Costo de Limpieza:** Estimación del esfuerzo que requerirá el **AI Analytics Engineer** para normalizar los datos.

---

> **Check de Certificación de Viabilidad:**
> - [ ] ¿Se ha notificado al estratega sobre cualquier KPI que sea imposible de medir hoy?
> - [ ] ¿El plan de mitigación es realista dado el stack tecnológico del SAD?
> - [ ] ¿He validado que no hay fugas de datos (Leakage) obvias en las fuentes de entrenamiento?

---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
