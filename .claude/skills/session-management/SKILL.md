---
name: session-management
description: Protocolo para la gestión sincronizada de apertura y cierre de sesiones de trabajo mediante Handoffs y Logs de Decisiones.
user-invocable: false
agent: ai-session-steward
allowed-tools: [Read, Write, Edit, Bash, notebooklm]
---

# Skill: Gestión de Sesión (Session Management)

Esta habilidad instrumenta los rituales definidos en **CLAUDE.md** para garantizar que la transición entre estados de desarrollo sea fluida, auditable y sin pérdida de contexto.

## Funciones

### 1. Ritual de Apertura (Session Kickoff)
**Acción:** `start_session`
- Lee `docs/references/handoff.md` para recuperar el estado operativo.
- Lee `docs/references/decisions.md` para asimilar el historial de decisiones y lecciones aprendidas.
- Lee `docs/references/config.md` para verificar la fase activa y los IDs de fuentes externas.
- Genera un **Briefing de Inicio** que resuma: "Dónde nos quedamos", "Lecciones clave para hoy", "Qué bloqueos tenemos" y "Cuál es la Tarea #1".

### 2. Ritual de Cierre (Session Wrap-up)
**Acción:** `close_session`
- **Generación de Handoff Operativo:** Sobrescribe `docs/references/handoff.md` con:
    - **Logros:** Entregables terminados en la sesión.
    - **Pendientes:** Tareas en curso o no iniciadas.
    - **Bloqueadores:** Falta de acceso, dudas de negocio o fallas técnicas.
    - **Próximos Pasos:** Hoja de ruta para la sesión inmediata.
- **Actualización de Memoria Histórica:** Añade una entrada (Appended) en `docs/references/decisions.md` con:
    - **Fecha:** Timestamp de la sesión.
    - **Fase:** Fase activa según el config.
    - **Decisiones:** Justificación de cambios estructurales o lógicos.
    - **Learnings:** Lecciones aprendidas (técnicas de datos, errores resueltos, etc.).
- **Sincronización con NotebookLM:** Leer el `NOTEBOOK_ID` y el nombre del notebook desde `docs/references/config.md` (sección 4.2 — Fuentes Externas). Actualizar únicamente los documentos que fueron **creados o modificados** durante la sesión. El mapa de documentos sincronizables es:

    | Documento | Ruta local | Cuándo sincronizar |
    | :-------- | :--------- | :----------------- |
    | `process.md` | `docs/methodology/process.md` | Si se modificó la metodología |
    | `brd.md` | `docs/governance/brd.md` | Si se creó o modificó el BRD |
    | `sad.md` | `docs/governance/sad.md` | Si se creó o modificó el SAD |
    | `specdd.md` | `docs/governance/specdd.md` | Si se creó o modificó el SpecDD |
    | `contract.md` | `docs/governance/contract.md` | Si se creó o modificó el contrato de datos |
    | `feasibility.md` | `docs/Phase_discovery/feasibility.md` | Si se creó o modificó el reporte de factibilidad |
    | `decisions.md` | `docs/references/decisions.md` | **Siempre** — se modifica en cada cierre |
    | `docs/changes/` | `docs/changes/CC-<ID>.md` | Si se creó o aprobó algún CC en la sesión |

    **Patrón de actualización por documento:**
    ```bash
    # NOTEBOOK_ID se obtiene leyendo docs/references/config.md (sección 4.2)
    NOTEBOOK_ID="<leer desde config.md>"
    # Reemplazar <titulo> por el nombre exacto del archivo (ej: "brd.md")
    notebooklm source delete-by-title "<titulo>" --notebook $NOTEBOOK_ID
    notebooklm source add <ruta_local> --notebook $NOTEBOOK_ID
    ```

    **Regla:** `decisions.md` se sincroniza en **todos** los cierres de sesión sin excepción. Las fichas de `docs/changes/` se sincronizan si hubo algún CC aprobado en la sesión. Los demás documentos solo si fueron tocados en la sesión actual.

## Criterios de Éxito
✅ **Continuidad Cognitiva:** Un nuevo agente debe ser capaz de retomar el trabajo leyendo únicamente el `handoff.md`.
✅ **Trazabilidad de Decisiones:** Cualquier cambio en el SAD o SpecDD debe tener una entrada correspondiente en el `decisions.md`.
✅ **Higiene de Archivos:** Las carpetas `docs/references/` contienen archivos actualizados y sin inconsistencias.
✅ **Sincronización NotebookLM:** Al finalizar cada cierre, `decisions.md` y todos los docs modificados en la sesión están actualizados en el notebook definido en `config.md` (sección 4.2).

## Reglas Técnicas
- **Formato Mandatorio:** Los archivos deben usar Markdown con tablas o listas para máxima legibilidad.
- **Append strictly:** El `decisions.md` nunca se sobrescribe; los nuevos registros se agregan al final con separadores claros.
- **Validation:** Antes de cerrar, el agente debe preguntar al usuario si hay algún "Insight" adicional que desee capturar para las lecciones aprendidas.


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
