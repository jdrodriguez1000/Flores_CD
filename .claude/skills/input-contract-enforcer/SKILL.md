---
name: input-contract-enforcer
description: Protocolo para la validación estricta de esquemas de entrada (Input Enforcement) en la capa de API para proteger la integridad del modelo.
user-invocable: false
agent: ai-backend-engineer
allowed-tools: [Read, Write, Edit, Python-Interpreter]
---

## 🏗️ I. Implementación de Pydantic Models
El agente debe construir la primera línea de defensa del sistema:
1. **Schema Rigidness:** Definir clases Pydantic para cada endpoint que validen tipos, rangos y obligatoriedad de campos según el SpecDD.
2. **Data Casting:** Asegurar que los datos recibidos (ej: strings que deberían ser floats) sean convertidos de forma segura o rechazados inmediatamente.
3. **Validación de Negocio Básica:** Implementar validadores (decoradores `@validator`) para chequeos rápidos (ej: "la fecha de inicio no puede ser posterior a la de fin").

## 📐 II. Sanitización y Seguridad
1. **Prevención de Inyección:** Limpiar inputs para evitar inyecciones SQL o de comandos en procesos que interactúen con el SO.
2. **Filtrado de Campos Extra:** Configurar los modelos para rechazar o ignorar campos que no estén en la especificación, evitando contaminación de datos.

## 🚀 III. Response Standardization
1. **Esquemas de Salida:** Garantizar que la API siempre devuelva una estructura predecible (ej: `{ "status": "success", "data": {...}, "error": null }`).
2. **Mapeo de Excepciones:** Convertir errores de validación de Pydantic en respuestas JSON legibles con el campo exacto que falló.

---

> **Check de Certificación de Contrato:**
> - [ ] ¿Todos los endpoints de entrada tienen un modelo Pydantic asociado?
> - [ ] ¿Se rechazan peticiones con tipos de datos incorrectos con un error 422 descriptivo?
> - [ ] ¿La validación de entrada es consistente con las restricciones de la Capa Bronze?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
