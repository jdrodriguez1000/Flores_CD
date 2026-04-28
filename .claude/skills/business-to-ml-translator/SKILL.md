---
name: business-to-ml-translator
description: Protocolo de traducción técnica para formalizar problemas de negocio en tareas de Machine Learning, seleccionando métricas de optimización basadas en el impacto financiero y el análisis del costo del error.
user-invocable: false
agent: ai-business-strategist
allowed-tools: [Read, Write, Edit]
---

## 🏗️ I. Encuadre Técnico del Problema (ML Framing)

El agente debe aplicar este protocolo para convertir el lenguaje coloquial en una tarea de ingeniería:

1. **Taxonomía de la Tarea:**
   - **Supervisado:** Clasificación (Binaria, Multi-clase, Multi-label) o Regresión.
   - **No Supervisado:** Clustering, Detección de Anomalías o Reducción de Dimensionalidad.
   - **Especializado:** Series temporales, Recomendación o NLP.
2. **Definición de la Variable Objetivo ($y$):** Identificar exactamente qué se quiere predecir y si esa variable está disponible en los sistemas actuales o debe ser construida (Labeling).
3. **Documentación Obligatoria:** El Business Requirements Document (BRD) debe guardarse en `docs/governance/brd.md`.

## 📐 II. Arquitectura de Métricas de Éxito

No basta con elegir una métrica; hay que justificarla mediante el **Análisis del Costo del Error**:

* **Optimización de Precisión ($Precision$):** Se activa cuando el costo de un "Falso Positivo" es prohibitivo (ej: Bloquear una transacción legítima de un cliente VIP).
* **Optimización de Sensibilidad ($Recall$):** Se activa cuando omitir un caso real ("Falso Negativo") es catastrófico (ej: No detectar una falla crítica en una turbina o un tumor en una radiografía).
* **Equilibrio ($F1\text{-score}$ o $G\text{-Mean}$):** Utilizado en datasets desbalanceados donde ambos errores tienen pesos similares.
* **Métricas de Error Continuo ($MAE, RMSE, MAPE$):** Para regresión, definiendo el umbral máximo de desviación aceptable para la toma de decisiones.

## 🚀 III. Análisis de Viabilidad y ROI (Costo-Beneficio)

1. **Cálculo de Baseline:** Establecer el rendimiento del proceso actual (ej: "Hoy los humanos aciertan el 60%").
2. **Impacto Estimado:** Proyectar cuánto dinero se ahorra o se gana por cada punto porcentual de mejora en la métrica técnica elegida.
3. **Identificación de Riesgos de Datos:** Alertar al **AI Data Analytics Consultant** si la variable objetivo es conocida por tener mucho ruido o retraso en su reporte (Lag).

---

> **Check de Certificación de Traducción:**
> - [ ] ¿La tarea de ML seleccionada cubre el 100% del caso de uso de negocio?
> - [ ] ¿He definido qué métrica técnica se usará para el ciclo "RED" de los agentes de QA?
> - [ ] ¿He cuantificado el costo monetario de un error del modelo?
> - [ ] ¿El BRD está guardado en `docs/governance/brd.md`?

---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
