---
name: change-control-management
description: Protocolo para la detección de derivas, evaluación de impacto y actualización formal de la línea base documental del proyecto.
user-invocable: false
agent: ai-change-manager
allowed-tools: [Read, Write, Edit, Bash]
---

# Skill: Gestión de Control de Cambios (Change Control)

Esta habilidad instrumenta la "Soberanía Documental" asegurando que cualquier cambio técnico sea validado contra la arquitectura y los requisitos de negocio.

## Funciones

### 1. Evaluación de Deriva Técnica
**Acción:** `evaluate_drift`
- Compara la intención del agente de ejecución contra el **SAD** y el **SpecDD**.
- Identifica qué secciones exactas de la documentación quedarían obsoletas con el cambio propuesto.

### 2. Generación de Ficha de CC
**Acción:** `generate_cc_proposal`
- Crea una propuesta estructurada en el chat para el usuario:
    - **CC-ID:** Identificador único.
    - **Cambio:** Descripción técnica clara.
    - **Justificación:** Por qué es mejor/necesario el cambio.
    - **Impacto:** Lista de documentos a actualizar (Ej: SAD pág 4, SpecDD endpoint X).

### 3. Ejecución de Efecto Cascada
**Acción:** `execute_approved_change`
- Actualiza los archivos de gobernanza (`.md`) con la nueva información.
- Crea la ficha formal del cambio en **`docs/changes/CC-<ID>.md`** con estructura estándar: CC-ID, Fecha, Estado, Cambio, Justificación, Impacto Transversal, Token de Continuidad.
- Registra una **referencia** al CC-ID (no el detalle completo) en `docs/references/decisions.md`.
- Emite un **"Token de Continuidad"** al agente original para que retome el código con la nueva especificación.

## Criterios de Éxito
✅ **Alineación 1:1:** Al finalizar el proceso, el código y los documentos de gobernanza vuelven a estar perfectamente sincronizados.
✅ **Autorización Explícita:** Existe evidencia en el chat de la aprobación manual del usuario para cada CC-ID.
✅ **Historial Intacto:** El `decisions.md` refleja la evolución real de la arquitectura del proyecto.

## Reglas Técnicas
- **Atomicidad:** Un CC debe tratar un solo cambio o grupo de cambios altamente relacionados. No mezclar cambios de negocio con refactores técnicos.
- **Rollback Mental:** Si el usuario rechaza el cambio, el agente debe ser capaz de sugerir la alternativa que más se acerque a la documentación original sin romper el sistema.


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
