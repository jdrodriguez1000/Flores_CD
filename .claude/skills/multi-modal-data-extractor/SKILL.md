---
name: multi-modal-data-extractor
description: Protocolo para la configuración y ejecución de extractores de datos de alto rendimiento para diversas fuentes (SQL, NoSQL, APIs, Streams).
user-invocable: false
agent: ai-data-engineer
allowed-tools: [Read, Write, Bash, Python-Interpreter, SQL]
---

## 🏗️ I. Configuración de Conectores
El agente debe implementar la lógica de conexión basándose en el tipo de fuente:
1. **Fuentes Estructuradas (SQL):**
   - Configuración de pools de conexión y ajustes de `fetch-size`.
   - Implementación de consultas incrementales basadas en "High Watermarks" (ej: `updated_at`, `id`).
2. **Fuentes No-Estructuradas/Documentales (NoSQL/APIs):**
   - Manejo de paginación y límites de tasa (Rate Limiting).
   - Implementación de lógica de reintentos (Exponential Backoff).
3. **Fuentes de Streaming:**
   - Configuración de consumidores (Kafka/PubSub) y gestión de offsets.

## 📐 II. Protocolos de Extracción
1. **Validación de Conexibilidad:** Antes de la extracción masiva, realizar un "heartbeat" o consulta de prueba limitada (`LIMIT 1`).
2. **Data Type Mapping:** Mapeo preventivo de tipos de datos de fuente a tipos compatibles con el entorno de destino (Parquet/Avro).
3. **Paralelización:** Definir la estrategia de particionamiento para extracciones masivas para evitar saturar la fuente.

## 🚀 III. Certificación de Extracción
El proceso termina cuando se genera un log de auditoría que confirma:
1. **Timestamp de Inicio/Fin.**
2. **Volumen de datos extraídos** (Bytes y registros).
3. **Estado de la extracción** (Éxito/Falla parcial).

---

> **Check de Certificación de Extracción:**
> - [ ] ¿Se han configurado las credenciales de forma segura (Secrets/Vault)?
> - [ ] ¿La extracción es incremental para evitar duplicidad innecesaria?
> - [ ] ¿Se manejan correctamente los tiempos de espera (timeouts) para evitar bloqueos?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
