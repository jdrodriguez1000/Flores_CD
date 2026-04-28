---
name: high-performance-api-builder
description: Protocolo para el desarrollo de APIs de alto rendimiento (FastAPI, gRPC) que integran los módulos de limpieza e inferencia en un flujo productivo.
user-invocable: false
agent: ai-backend-engineer
allowed-tools: [Read, Write, Edit, Bash, Python-Interpreter]
---

## 🏗️ I. Diseño de Endpoints de IA
El agente debe construir los puntos de acceso al sistema:
1. **Rutas de Inferencia:** Implementar endpoints que reciban el JSON de entrada, ejecuten el preprocesamiento (Phase Engineering) y devuelvan el resultado del modelo (Phase Modeling).
2. **Modularidad Funcional:** Asegurar que la API importe los módulos `.py` certificados por el ML Engineer y no duplique lógica.
3. **Manejo de Estados de Salud (Healthchecks):** Implementar rutas `/health` y `/ready` que verifiquen la conexión a BD y la carga correcta del modelo.

## 📐 II. Optimización del Request/Response
1. **Serialización Eficiente:** Uso de Pydantic para el parseo de datos y optimización de la respuesta para minimizar el tamaño del payload.
2. **Caching de Resultados:** Implementar estrategias de caché (Redis) para peticiones repetitivas con los mismos parámetros de entrada.
3. **Control de Latencia:** Asegurar que el overhead de la API sea mínimo (<10ms) sumado al tiempo de inferencia del modelo.

## 🚀 III. Documentación Automática (OpenAPI)
1. **Esquemas Claros:** Generar documentación (Swagger/Redoc) detallada con ejemplos de entrada y códigos de error.
2. **Codes de Error Semánticos:** Diferenciar entre errores de validación (422), errores de datos (400) y fallas del servidor (500).

---

> **Check de Certificación de API:**
> - [ ] ¿Los endpoints integran los módulos certificados de las Fases 2 y 3?
> - [ ] ¿La documentación de OpenAPI refleja fielmente el Contrato de Datos?
> - [ ] ¿Se han implementado los healthchecks necesarios para el orquestador?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
