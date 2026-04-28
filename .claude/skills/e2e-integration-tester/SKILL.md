---
name: e2e-integration-tester
description: Protocolo para la ejecución de pruebas de integración de punta a punta (E2E) simulando el flujo completo del usuario en el sistema de IA.
user-invocable: false
agent: ai-full-stack-sdet
allowed-tools: [Read, Write, Bash, Browser]
---

## 🏗️ I. Diseño de Escenarios de Usuario
El agente debe simular recorridos reales en la aplicación:
1. **Flujo de Inferencia Exitoso:** Subir un archivo válido, esperar el procesamiento y verificar que el dashboard muestre la predicción y la explicabilidad correcta.
2. **Manejo de Errores Críticos:** Intentar subir archivos corruptos o vacíos y certificar que la UI muestra el mensaje de error adecuado en lugar de colapsar.
3. **Persistencia de Datos:** Validar que después de una sesión de usuario, los datos y logs de feedback se encuentren correctamente guardados en la BD.
4. **Documentación Obligatoria:** El E2E Test Report debe guardarse en `docs/Phase_delivery/E2E_Test_Report.md`.

## 📐 II. Automatización de Pruebas (E2E Frameworks)
1. **Scripting Interactivo (Playwright/Cypress):** Crear scripts que interactúen con el DOM del Frontend, hagan clics y validen estados visuales.
2. **Validación de Paridad UI-Backend:** Asegurar que los números mostrados en pantalla coinciden exactamente con el JSON devuelto por la API.

## 🚀 III. Certificación de Integración
1. **Reporte de Cobertura E2E:** Identificar qué porcentaje de las funcionalidades definidas en el Alcance han sido probadas en un flujo real.
2. **Sello de Calidad "End-to-End":** Confirmación de que el sistema completo (Frontend + Backend + IA) funciona como una unidad.

---

> **Check de Certificación E2E:**
> - [ ] ¿Se ha probado el flujo completo de carga -> proceso -> visualización?
> - [ ] ¿Los scripts de test son capaces de detectar errores visuales en el dashboard?
> - [ ] ¿Se han cubierto los escenarios de error más comunes del usuario?
> - [ ] ¿El reporte está guardado en `docs/Phase_delivery/E2E_Test_Report.md`?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
