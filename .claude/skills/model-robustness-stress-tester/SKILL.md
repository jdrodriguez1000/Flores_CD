---
name: model-robustness-stress-tester
description: Protocolo para la evaluación de la robustez del modelo ante datos ruidosos, anomalías y escenarios fuera de distribución (OOD).
user-invocable: false
agent: ai-model-qa-validator
allowed-tools: [Read, Write, Python-Interpreter]
---

## 🏗️ I. Inyección de Ruido y Corrupción de Datos
El agente debe simular condiciones reales adversas:
1. **Atuación de Ruido Blanco:** Introducir pequeñas variaciones en las variables numéricas para ver si la predicción cambia erráticamente.
2. **Missing Values Stress:** Evaluar cómo responde el modelo si faltan datos en campos no obligatorios pero importantes.
3. **Outlier Sensitivity:** Verificar si el modelo es demasiado sensible a valores extremos y si estos rompen la lógica de predicción.

## 📐 II. Evaluación Fuera de Distribución (OOD)
1. **Detección de Drift de Entrada:** Simular datos de una ventana temporal diferente o un mercado distinto para medir la degradación.
2. **Adversarial Examples:** Intentar confundir al modelo con cambios mínimos diseñados para provocar una predicción incorrecta (especialmente en Deep Learning).

## 🚀 III. Certificación de Resiliencia
1. **Mapa de Estabilidad Predictiva:** Identificar bajo qué condiciones técnicas exactas el modelo deja de ser confiable.
2. **Manejo de Errores Técnicos:** Confirmar que ante datos corruptos el modelo emite el error correcto definido en el SAD en lugar de dar una predicción "alucinada".

---

> **Check de Certificación de Robustez:**
> - [ ] ¿Se ha evaluado la respuesta del modelo ante un 10%, 20% y 30% de ruido en las variables?
> - [ ] ¿El modelo es estable ante valores extremos controlados?
> - [ ] ¿Existe un plan de contingencia para datos fuera de distribución?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
