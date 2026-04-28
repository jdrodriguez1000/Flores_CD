---
name: diagnostic-data-auditor
description: Protocolo para la ejecución del Exploratory Data Quality (EDQ), diagnóstico de salud estadística y catalogación técnica de activos de datos multi-fuente.
user-invocable: false
agent: ai-data-auditor
allowed-tools: [Read, Write, Bash, Python-Interpreter, SQL]
---

## 🏗️ I. Inventario de Fuentes y Conectividad

El agente debe mapear el ecosistema de datos siguiendo estos pasos:
1. **Catalogación Técnica:** Identificar tipos de fuentes (Tablas SQL, colecciones NoSQL, endpoints de APIs, archivos planos).
2. **Perfilado de Esquema:** Documentar tipos de datos detectados vs. tipos de datos esperados (formatos de fecha, precisión de decimales, codificación de texto).
3. **Volumetría Histórica:** Evaluar si la cantidad de registros es suficiente para la tarea de ML (ej: ¿Hay suficientes casos de fraude para entrenar una clasificación?).
4. **Documentación Obligatoria:** El Data Feasibility Report debe guardarse en `docs/Phase_discovery/Feasibility_Report.md`.

## 📐 II. Análisis Exploratorio de Calidad (EDQ)

Ejecución de un diagnóstico profundo sobre la salud de los datos:
* **Integridad:** Cálculo de porcentajes de valores nulos, vacíos o "placeholder" (ej: "9999", "N/A") por variable.
* **Validez y Ruido:** Detección de *outliers* técnicos (ej: edades negativas, precios de cero) y duplicidad de registros.
* **Consistencia:** Verificar si los datos significan lo mismo en diferentes fuentes (ej: ¿El `customer_id` de la API coincide con el de la DB?).
* **Distribución Inicial:** Análisis de desbalance de clases para variables objetivo y variabilidad de las variables predictivas ($X$).

## 🚀 III. Generación de Metadatos de Calidad

1. **Scorecard de Salud:** Asignar una calificación de 1 a 10 a cada fuente de datos basada en su limpieza.
2. **Diccionario de Auditoría:** Documentar qué significa cada columna desde una perspectiva técnica y qué problemas potenciales se encontraron.
3. **Muestreo de Verdad:** Extraer ejemplos representativos para que el **AI Solutions Architect** pueda diseñar los contratos de datos en el SpecDD.

---

> **Check de Certificación de Auditoría:**
> - [ ] ¿He identificado todas las fuentes necesarias para construir la variable objetivo?
> - [ ] ¿He detectado patrones de nulos que sigan una lógica de error de sistema (Missing Not At Random)?
> - [ ] ¿La volumetría de datos soporta estadísticamente la complejidad del modelo propuesto?

---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
