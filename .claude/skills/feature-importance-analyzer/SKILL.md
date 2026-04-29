---
name: feature-importance-analyzer
description: Protocolo para el análisis de la importancia de variables y explicabilidad (XAI) mediante SHAP, Permutation Importance y pesos intrínsecos, aplicado comparativamente al Modelo Control y al Modelo Tratamiento del Shadow Test.
user-invocable: false
agent: ai-data-scientist
allowed-tools: [Read, Write, Python-Interpreter]
---

## 🏗️ I. Análisis Global de Importancia (por Modelo)
Ejecutar el análisis **de forma separada** para Control y Tratamiento, luego comparar:

1. **Permutation Importance:** Evaluar la caída en el rendimiento al aleatorizar una variable (método agnóstico al modelo). Aplicar sobre el mismo conjunto de validación para ambos modelos.
2. **Importancia Intrínseca:** Uso de Gini Importance o pesos de coeficientes (si el modelo lo permite).
3. **Análisis de Residuos:** Identificar en qué segmentos de las variables cada modelo falla más.

### Comparativa de Rankings de Importancia
Documentar si los dos modelos aprenden las mismas señales o divergen:

| Variable | Importancia Control (rank) | Importancia Tratamiento (rank) | Divergencia |
|---|---|---|---|
| feature_A | 1 | 1 | Coinciden |
| feature_B | 3 | 8 | Divergen — investigar |

Una divergencia alta entre rankings indica que el Tratamiento puede estar capturando señales menos interpretables o con mayor riesgo de leakage.

## 📐 II. Explicabilidad Local (SHAP Values — ambos modelos)
Implementar SHAP para entender cada predicción individual:

1. **Summary Plots comparativos:** Generar un summary plot por modelo. Visualizar cómo cada variable empuja la predicción en Control vs. Tratamiento.
2. **Force Plots en casos de desacuerdo:** Para las predicciones donde Control y Tratamiento difieren (base del Shadow Test), aplicar SHAP local para explicar por qué cada modelo decidió diferente.
3. **Interdependencias:** Detectar interacciones entre variables que el modelo ha aprendido (ej: la variable A solo importa si la B es > X). Comparar si estas interacciones son las mismas en ambos modelos.
4. **Validación de Sentido Común:** Confirmar con el Estratega de Negocio que las variables más importantes tienen sentido lógico en el dominio — especialmente si el Tratamiento prioriza variables que el Control ignora.

## 🚀 III. Informe de Transparencia Predictiva

1. **Top N Variables por Modelo:** Lista priorizada para la visualización en el Frontend, separada por rol (`control_top_features`, `treatment_top_features`).
2. **Veredicto de Justificabilidad:** Confirmar que ningún modelo toma decisiones basadas en ruido o variables no éticas. El Tratamiento (que puede ser más complejo) tiene mayor riesgo en este punto.
3. **Señal de Alerta para Shadow Test:** Si la distribución SHAP del Tratamiento es radicalmente distinta a la del Control, documentarlo como riesgo en la ficha del Shadow Test — indica que el Challenger puede estar sobre-ajustando a patrones espurios.

---

> **Check de Certificación de Importancia:**
> - [ ] ¿Se han aplicado SHAP y Permutation Importance a **ambos** modelos (Control y Tratamiento)?
> - [ ] ¿Se ha documentado la comparativa de rankings entre Control y Tratamiento?
> - [ ] ¿Las divergencias en rankings están justificadas o investigadas?
> - [ ] ¿Se han identificado variables que dominan el Tratamiento de forma sospechosa (posible Leakage)?
> - [ ] ¿La importancia de las variables coincide con las hipótesis de negocio de la Phase Discovery?
> - [ ] ¿Se ha emitido la señal de alerta para el Shadow Test si los patrones SHAP divergen significativamente?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
