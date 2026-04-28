---
name: experiment-tracking-configurator
description: Protocolo para la configuración y auditoría de sistemas de seguimiento de experimentos (MLflow, Weights & Biases) para garantizar la trazabilidad de métricas y artefactos.
user-invocable: false
agent: ai-mlops-specialist
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Configuración del Servidor de Tracking
El agente debe asegurar que el entorno de experimentación sea persistente y compartido:
1. **Configuración de Backend Store:** Establecer la base de datos (Postgres/MySQL) para el almacenamiento de parámetros y métricas.
2. **Artifact Store:** Configurar el almacenamiento de objetos (S3, GCS, Local) para guardar diccionarios, modelos y plots.
3. **Control de Acceso:** Garantizar que los agentes `ai-data-scientist` y `ai-ml-engineer` tengan los permisos necesarios para registrar ejecuciones.

## 📐 II. Estandarización de Registros (Naming Conventions)
1. **Jerarquía de Experimentos:** Agrupar ejecuciones por proyecto y fase (ej: `Inference-Optimization-v1`).
2. **Logging Obligatorio:** Auditar que cada ejecución registre:
   - Commit de Git (Code Version).
   - Hiperparámetros finales.
   - Curvas de aprendizaje (Lost/Accuracy per epoch).
   - Tags de usuario y agente responsable.

## 🚀 III. Auditoría de Salud del Tracking
1. **Consistencia de Métricas:** Verificar que no existan experimentos con nombres duplicados o métricas mal reportadas.
2. **Limpieza de Experimentos Huérfanos:** Eliminar o archivar ejecuciones fallidas o vacías para mantener el registro limpio.

---

> **Check de Certificación de Tracking:**
> - [ ] ¿El servidor de tracking es accesible para todo el equipo de fase 3?
> - [ ] ¿Todas las ejecuciones incluyen el hash del código fuente para reproducibilidad?
> - [ ] ¿Los artefactos críticos (modelos) se están guardando en el store centralizado?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
