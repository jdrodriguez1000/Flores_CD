---
name: cleansing-bias-detector
description: Protocolo para la detección de sesgos y cambios de distribución (EDQ de Transformación) introducidos durante la limpieza y normalización de datos.
user-invocable: false
agent: ai-analytics-engineer
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Análisis de Distribución Pre vs. Post
El agente debe comparar estadísticamente el estado de los datos antes y después de la limpieza:
1. **Auditoría de Transformaciones:** Documentar cada regla de limpieza aplicada (imputación, filtrado, normalización).
2. **Detección de Sesgos Técnicos:** Identificar si la limpieza está eliminando registros de forma desproporcionada en ciertos subgrupos.
3. **Validación de Consistencia:** Asegurar que los datos en Silver sigan las reglas de negocio definidas.
4. **Documentación Obligatoria:** El reporte de EDA de Limpieza debe guardarse en `docs/Phase_engineering/EDA_Limpieza.md`.

## 📐 II. Validación de Integridad Estadística
1. **Preservación de Varianza:** Garantizar que la limpieza de ruido no haya "aplanado" la señal necesaria para el modelo de ML.
2. **Test de Hipótesis:** Realizar pruebas (ej: Kolmogorov-Smirnov) para confirmar si las muestras antes/después pertenecen a la misma distribución de base, o si el cambio es significativo.

## 🚀 III. Informe de Certificación de Calidad
Generar un diagnóstico que incluya:
1. **Tasa de Descarte:** Porcentaje de registros eliminados y justificación.
2. **Alertas de Sesgo:** Identificación de variables cuya distribución se alteró más allá del umbral aceptable (ej: >5%).

---

> **Check de Certificación de Sesgo:**
> - [ ] ¿La eliminación de duplicados ha sesgado la distribución de la variable objetivo?
> - [ ] ¿Se ha cuantificado el impacto de la normalización en la varianza de los datos?
> - [ ] ¿El informe de transformación ha sido firmado y notificado al Feature Architect?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
