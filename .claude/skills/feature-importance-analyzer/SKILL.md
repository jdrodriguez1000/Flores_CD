---
name: feature-importance-analyzer
description: Protocolo para el análisis de la importancia de variables y explicabilidad del modelo (XAI) mediante SHAP, Permutation Importance y pesos intrínsecos.
user-invocable: false
agent: ai-data-scientist
allowed-tools: [Read, Write, Python-Interpreter]
---

## 🏗️ I. Análisis Global de Importancia
El agente debe cuantificar cómo contribuye cada feature de la Capa Gold al resultado del modelo:
1. **Permutation Importance:** Evaluar la caída en el rendimiento al aleatorizar una variable (método agnóstico al modelo).
2. **Importancia Intrínseca:** Uso de Gini Importance o pesos de coeficientes (si el modelo lo permite).
3. **Análisis de Residuos:** Identificar en qué segmentos de las variables el modelo está fallando más.

## 📐 II. Explicabilidad Local (SHAP Values)
Implementar técnicas de teoría de juegos (SHAP) para entender cada predicción individual:
1. **Force Plots / Summary Plots:** Visualizar cómo cada variable empuja la predicción hacia arriba o hacia abajo.
2. **Interdependencias:** Detectar interacciones entre variables que el modelo ha aprendido (ej: la variable A solo importa si la B es > X).
3. **Validación de Sentido Común:** Confirmar con el Estratega de Negocio que las variables más importantes tienen sentido lógico en el dominio.

## 🚀 III. Informe de Transparencia Predictiva
Generar el artefacto de XAI:
1. **Top N Variables:** Lista priorizada para la visualización en el Frontend (Phase Delivery).
2. **Veredicto de Justificabilidad:** Confirmación de que el modelo no está tomando decisiones basadas en ruido o variables no éticas.

---

> **Check de Certificación de Importancia:**
> - [ ] ¿Se han identificado variables que dominan el modelo de forma sospechosa (posible Leakage)?
> - [ ] ¿La importancia de las variables coincide con las hipótesis de negocio de la Phase Discovery?
> - [ ] ¿Se ha proporcionado la lógica necesaria para que el Frontend explique la predicción al usuario?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
