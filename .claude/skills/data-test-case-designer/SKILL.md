---
name: data-test-case-designer
description: Protocolo para la traducción de hallazgos analíticos (EDA) en suites de pruebas unitarias e integrales para garantizar la estabilidad del pipeline.
user-invocable: false
agent: ai-data-qa-engineer
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🏗️ I. Traducción de EDA a Tests
El agente debe convertir los umbrales estadísticos identificados en los tres momentos del EDA en casos de prueba automatizados:
1. **Tests de Ingesta:** Basados en el EDA de Ingesta, crear pruebas que validen la volumetría y la integridad de tipos (ej: "Falla si el archivo tiene 0 registros").
2. **Tests de Transformación:** Basados en el EDA de Limpieza, crear pruebas que validen la ausencia de ruidos técnicos eliminados (ej: "Falla si el campo `precio` tiene valores negativos").
3. **Tests Estadísticos:** Basados en el EDA Estadístico, crear pruebas de distribución (ej: "Falla si el promedio de `X` cambia más de 2 sigmas respecto al histórico").

## 📐 II. Orquestación del Ciclo TDD
1. **Fase RED:** Escribir el test antes de que el módulo sea desarrollado, asegurando que capture la falla lógica.
2. **Fase GREEN:** Confirmar que el código mínimo del ingeniero hace pasar el test.
3. **Fase REFACTOR:** Validar que las optimizaciones de código no rompan la lógica de los datos.

## 🚀 III. Gestión de la Suite de Pruebas
1. **Categorización:** Organizar tests en unitarios, de integración y estadísticos.
2. **Reporte de Cobertura:** Asegurar que el 100% de los requisitos definidos en el Contrato de Datos estén cubiertos por al menos una validación técnica.

---

> **Check de Certificación de Tests:**
> - [ ] ¿Se han cubierto todos los puntos críticos del EDA de las capas Bronze y Silver?
> - [ ] ¿Los tests son determinísticos y repetibles?
> - [ ] ¿Se ha incluido un test para cada restricción del Contrato de Datos?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
