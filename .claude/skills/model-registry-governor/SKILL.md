---
name: model-registry-governor
description: Protocolo para el control de gobernanza del Model Registry, gestionando estados de ciclo de vida (Staging, Production, Archive).
user-invocable: false
agent: ai-mlops-specialist
allowed-tools: [Read, Write, Bash, Python-Interpreter]
---

## 🏗️ I. Políticas de Promoción de Modelos
El agente debe definir los criterios para que un modelo cambie de estado:
1. **De None a Staging:** Requiere que el QA Engineer haya certificado los tests técnicos y el Data Scientist las métricas.
2. **De Staging a Production:** Requiere una auditoría de latencia por parte del ML Engineer y la aprobación del Solutions Architect.
3. **Rollback Policy:** Definir cómo revertir a una versión anterior (`Archive`) si se detecta degradación en producción.

## 📐 II. Gestión de Versiones y Aliases
1. **Semantic Versioning:** Aplicar reglas de versionado (Major.Minor.Patch) según el impacto del cambio en los datos o el código.
2. **Aliases de Producción:** Mantener el tag `Production` apuntando siempre a la última versión estable sin necesidad de cambiar los endpoints de la API.

## 🚀 III. Seguridad y Cumplimiento del Registro
1. **Bloqueo de Versiones:** Impedir que versiones en `Production` sean eliminadas o modificadas accidentalmente.
2. **Histórico de Cambios:** Mantener un log de qué agente promovió qué versión y bajo qué justificación técnica.

---

> **Check de Certificación de Gobernanza:**
> - [ ] ¿Existe una política clara de promoción de modelos documentada?
> - [ ] ¿Los tags de Staging y Production son únicos y están actualizados?
> - [ ] ¿Se puede rastrear qué agente realizó el último cambio de estado en el registro?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
