---
name: project-config
description: Protocolo para inicializar, actualizar y auditar la configuración central del proyecto (Cédula de Identidad).
user-invocable: false
agent: config-manager-agent
allowed-tools: [Read, Write, Edit, Bash]
---

# Skill: Gestión de Configuración de Proyecto (Setup)

Esta habilidad es la dueña de la integridad del archivo **docs/references/config.md**. Su propósito es instanciar las reglas generales de **CLAUDE.md** en un contexto local específico para definir la identidad y las fuentes de verdad.

## Jerarquía de Verdad
1. **CLAUDE.md**: Fuente suprema de protocolos y convenciones agnósticas.
2. **config.md**: Implementación local y depósito de metadatos específicos del proyecto.

## Modos de Operación

### Modo 1: Inicialización (Bootstrap)
**Condición:** Proyecto nuevo, no existe docs/references/config.md
**Acciones:**
- Verifica que `docs/governance/backlog.md` exista (generado en el bootstrap del repo). Si no existe, detener y alertar al usuario para ejecutar primero `repository-governance.initialize_repo`.
- Marca la tarea **[F1-T01]** en `docs/governance/backlog.md` como `IN_PROGRESS` antes de comenzar.
- Crea la estructura de directorios `docs/references/` si no existe.
- Interroga al usuario por la Identidad y Fuentes de Verdad.
- Genera el archivo con el encabezado de definición estándar.
- Al finalizar, marca **[F1-T01]** como `DONE` en `docs/governance/backlog.md`.

### Modo 2: Gestión de Estado y Fuentes
**Condición:** Cambio de IDs de fuentes externas, repositorios o avance de Fase.
**Acciones:**
- Actualiza la sección **🌍 Fuentes de Verdad**.
- Actualiza la sección **📈 Estado del Proyecto**.

## Estructura Mandatoria del config.md

El archivo debe seguir este orden estricto de secciones:
1. **Definición del Documento**: Encabezado que referencia a CLAUDE.md.
2. **Identidad del Proyecto**: Metatabla de nombre, alias y propietario.
3. **Estado del Proyecto**: Fase actual, hito activo y progreso porcentual.
4. **Fuentes de Verdad**: Tablas con IDs de fuentes externas (NotebookLM, Wikis) y URLs de Git.

## Protocolo de Actualización
1. **Lectura Previa**: Siempre leer el estado actual antes de modificar.
2. **Validación de Ruta**: Forzar siempre la escritura en `docs/references/config.md`.

## Criterios de Éxito
✅ **Centralización**: Todo ID externo (NotebookLM, Notion, Git) debe residir aquí.
✅ **Veracidad**: 100% de la información proviene de CLAUDE.md o del Interrogatorio Directo.

## Notas Críticas
- **Prohibición de Alucinación**: La habilidad fallará si intenta escribir un campo obligatorio sin tener la respuesta explícita del usuario.
- **Ruta Fija**: Siempre `docs/references/config.md`.



---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
