---
name: statistical-gold-auditor
description: Protocolo para la validación estadística de la capa Gold, detección de fuga de datos (Target Leakage) y análisis de multicolinealidad.
user-invocable: false
agent: ai-feature-store-architect
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Detección de Fuga de Información (Target Leakage)
El agentes debe garantizar que ninguna variable en $X$ contenga información futura o derivada de $y$:
1. **Análisis de Correlación:** Evaluar la relación entre las nuevas variables (Features) y la variable objetivo ($y$).
2. **Evaluación de Importancia:** Rankear las variables generadas según su poder predictivo preliminar.
3. **Control de Estacionariedad:** Verificar que las variables no tengan derivas temporales drásticas que afecten el modelo.
4. **Documentación Obligatoria:** El reporte de EDA Estadístico debe guardarse en `docs/Phase_engineering/EDA_Estadistico.md`.
5. **Análisis de Correlación Temporal:** Verificar que los datos usados para predecir $y$ sean estrictamente anteriores al evento.
6. **Identificación de Variables "Gemelas":** Detectar variables que tienen una correlación casi perfecta (>0.99) con el objetivo y que podrían ser proxies del resultado.

## 📐 II. Análisis de Multicolinealidad y Relevancia
1. **VIF (Variance Inflation Factor):** Calcular el VIF para eliminar variables redundantes que aporten el mismo valor estadístico.
2. **Análisis de Importancia Predictiva:** Uso de métodos como *Mutual Information* o *ANOVA* para validar que la relación entre $X$ e $y$ es estadísticamente significativa antes de pasar a la Phase Modeling.

## 🚀 III. Informe de Salud de la Capa Gold
Generar el diagnóstico final de datos para entrenamiento:
1. **Matriz de Correlación Completa.**
2. **Certificación de No-Leakage:** Declaración explícita de que no se ha detectado fuga de información.
3. **Selección de Variables Base:** Lista de variables recomendadas para el baseline del Científico de Datos.

---

> **Check de Certificación Gold:**
> - [ ] ¿Se ha realizado el análisis de Target Leakage sobre todas las variables nuevas?
> - [ ] ¿El VIF es < 10 para todas las variables seleccionadas para el modelo?
> - [ ] ¿Se ha validado la significancia estadística de la variable objetivo ($y$)?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
