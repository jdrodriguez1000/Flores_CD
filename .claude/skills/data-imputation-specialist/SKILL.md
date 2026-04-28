---
name: data-imputation-specialist
description: Protocolo para la selección y aplicación de estrategias técnicas de imputación de valores faltantes para maximizar la integridad del dataset.
user-invocable: false
agent: ai-analytics-engineer
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🏗️ I. Selección de Estrategia de Imputación
El agente debe decidir el método basándose en la naturaleza del dato y el hallazgo del Auditor:
1. **Imputación Estadística Simple:** Uso de Media (datos normales), Mediana (con outliers) o Moda (categóricos).
2. **Imputación de Negocio:** Uso de valores constantes o reglas lógicas (ej: si `es_propietario` es nulo pero tiene `hipoteca`, poner `True`).
3. **Imputación Avanzada:** Uso de algoritmos (Iterative Imputer, KNN) cuando la variable es crítica y su relación con otras variables es alta.

## 📐 II. Marcaje de Datos Imputados
Es obligatorio mantener la trazabilidad de qué registros fueron modificados:
1. **Inputation Flags:** Crear columnas booleanas (`_is_imputed_{columna}`) para registrar que el valor original no existía.
2. **Preservación del Original:** En la capa Silver, se recomienda mantener la columna original con nulos y crear una nueva con el valor imputado para auditorías.

## 🚀 III. Validación del Impacto de la Imputación
1. **Detección de "Valores Fantasma":** Asegurar que la imputación no cree patrones falsos que el modelo de ML pueda aprender erróneamente (ej: picos artificiales en la media).
2. **Consistencia Lógica:** Validar que el valor imputado sea físicamente posible (ej: no imputar edad de 150 años).

---

> **Check de Certificación de Imputación:**
> - [ ] ¿Se ha usado la estrategia de imputación acordada en el Data Feasibility Report?
> - [ ] ¿Los flags de imputación están presentes para el Feature Engineer?
> - [ ] ¿La distribución de la variable permanece coherente tras la imputación masiva?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
