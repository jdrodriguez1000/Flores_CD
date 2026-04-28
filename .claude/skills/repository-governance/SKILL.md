---
name: repository-governance
description: Protocolo técnico para la organización, limpieza y gestión de versiones de un repositorio de Ciencia de Datos e IA.
user-invocable: false
agent: ai-repository-governor
allowed-tools: [Read, Write, Edit, Bash]
---

# Skill: Gobernanza de Repositorio (Repository Governance)

Esta habilidad instrumenta las reglas de **GEMINI.md** y la metodología de 4 fases para garantizar que el repositorio sea un activo técnico de alta calidad.

## Funciones Principales

### 1. Bootstrap de Estructura Industrial
**Acción:** `initialize_repo`
- **Fuente de Verdad Operativa:** Leer obligatoriamente `docs/references/directory.md`.
- **Ejecución:** Replicar de forma recursiva la estructura detallada en las secciones "Estructura Raíz" y "Detalle de Subdirectorios" de `directory.md`. Esto incluye la creación de:
    - Capas de datos (Bronze, Silver, Gold).
    - Fases de documentación (Phase_discovery a Phase_delivery).
    - Módulos de código (`src/data`, `src/features`, `src/models`, `src/api`, `src/core`).
    - Suites de pruebas (`tests/unit`, `tests/integration`, `tests/e2e`, `tests/model_qa`).
- **Persistencia:** Cada carpeta creada debe incluir un archivo `.gitkeep` si está vacía.
- **Configuración Base:** 
    - Crear el `.gitignore` estándar para DS (excluyendo `.csv`, `.parquet`, `.pkl`, `.h5`, `.env`, `__pycache__`, etc.).
    - Crear un `README.md` base con la ficha técnica del proyecto.
- **Entregable Obligatorio:** Al finalizar la creación de carpetas, genera automáticamente `docs/governance/backlog.md` con el roadmap inicial de **Phase Discovery completo** (ver sección *Backlog Inicial de Phase Discovery* más abajo). Este es el primer artefacto del proyecto y hoja de ruta para el equipo.

### Backlog Inicial de Phase Discovery (Generado en Bootstrap)

El archivo `docs/governance/backlog.md` debe ser creado con la siguiente estructura al ejecutar `initialize_repo`:

```markdown
# Backlog del Proyecto

> Generado automáticamente en el Bootstrap del repositorio.
> Fuente de verdad: GEMINI.md | Metodología: process.md

---

## FASE 1: Discovery — Línea Base Documental

**Entregable Principal:** Documentación de Gobernanza completa (config + BRD + Factibilidad + Mockup + SAD + SpecDD + Contract).

### Iteración 1.1: Configuración e Identidad del Proyecto

#### [F1-T01] Crear config.md
- **Responsable:** @config-manager
- **Iteración:** 1.1
- **Entregable:** `docs/references/config.md`
- **Acción:** Documentation
- **DoD:** El archivo existe con las 4 secciones mandatorias (Definición, Identidad, Estado, Fuentes). Todos los campos obligatorios tienen valor real (no placeholder).
- **Estado:** TODO

### Iteración 1.2: Documentación de Negocio

#### [F1-T02] Crear BRD (Business Requirements Document)
- **Responsable:** @ai-business-strategist
- **Iteración:** 1.2
- **Entregable:** `docs/governance/brd.md`
- **Acción:** Documentation
- **DoD:** BRD contiene objetivos de negocio, KPIs con thresholds definidos y criterios de aceptación verificables.
- **Estado:** TODO

### Iteración 1.3: Factibilidad y Diseño de Experiencia

#### [F1-T03] Ejecutar Análisis de Factibilidad
- **Responsable:** @ai-data-auditor
- **Iteración:** 1.3
- **Entregable:** `docs/Phase_discovery/feasibility.md`
- **Acción:** Documentation
- **DoD:** Reporte incluye diagnóstico de calidad de datos (completitud, distribución, outliers) y veredicto GO/NO-GO para continuar a Phase Engineering.
- **Estado:** TODO

#### [F1-T04] Crear Mockup de Interfaz
- **Responsable:** @ai-ux-designer
- **Iteración:** 1.3
- **Entregable:** `docs/Phase_discovery/mockup.md`
- **Acción:** Documentation
- **DoD:** Mockup aprobado por el Stakeholder principal. Cubre flujos principales de la aplicación.
- **Estado:** TODO

### Iteración 1.4: Arquitectura y Especificaciones Técnicas

#### [F1-T05] Crear SAD (Software Architecture Document)
- **Responsable:** @ai-solutions-architect
- **Iteración:** 1.4
- **Entregable:** `docs/governance/sad.md`
- **Acción:** Documentation
- **DoD:** SAD define el stack tecnológico, diagrama de arquitectura de 4 capas (Bronze/Silver/Gold/Model) y las interfaces entre componentes.
- **Estado:** TODO

#### [F1-T06] Crear SpecDD (Specification-Driven Development)
- **Responsable:** @ai-solutions-architect
- **Iteración:** 1.4
- **Entregable:** `docs/governance/specdd.md`
- **Acción:** Documentation
- **DoD:** SpecDD contiene las firmas de todas las funciones `.py` del pipeline, contratos de entrada/salida y criterios de aceptación técnicos por módulo.
- **Estado:** TODO

### Iteración 1.5: Contrato de Datos

#### [F1-T07] Crear Data Contract
- **Responsable:** @ai-data-auditor
- **Iteración:** 1.5
- **Entregable:** `docs/governance/contract.md`
- **Acción:** Documentation
- **DoD:** Contract define esquema de variables (tipo, rango, cardinalidad), reglas de validación matemáticas y criterios de rechazo de datos.
- **Estado:** TODO

---

## FASE 2: Engineering — Feature Set Certificado
> Tareas pendientes de atomización. Se poblará al completar Phase Discovery.

## FASE 3: Modeling — Modelo Predictivo Certificado
> Tareas pendientes de atomización. Se poblará al completar Phase Engineering.

## FASE 4: Delivery — Sistema en Producción
> Tareas pendientes de atomización. Se poblará al completar Phase Modeling.
```

### 2. Auditoría de Higiene Git
**Acción:** `audit_git_health`
- **Check Large Files:** Escanea el área de *stage* buscando archivos > 10MB. Si existen, sugiere DVC o eliminación.
- **Check Notebooks:** Verifica si los `.ipynb` tienen la celda de output poblada. Si es así, ejecuta `nbstripout` o pide al usuario limpiar antes de commitear.
- **Check Branches:** Valida que la rama actual siga la jerarquía mandatoria:
    - `main` | `dev`
    - `iteracion/X.Y` (ej: `iteracion/2.1`)
    - `slice/F[1-4]-<nombre>` (ej: `slice/F2-ingesta`)

### 3. Orquestación de Commits y PRs
**Acción:** `semantic_commit_manager`
- Valida que el mensaje de commit empiece con: `feat(data):`, `feat(model):`, `feat(api):`, `test(qa):`, `docs(f-X):`.
- Genera el borrador del Pull Request vinculándolo a los documentos de la Fase activa (ej: "Resolves tasks defined in sad.md").

### 4. Cumplimiento de Linaje (Phase Modeling)
**Acción:** `verify_lineage_link`
- Asegura que al commitear un cambio en `src/`, se reporte si hay un impacto en la versión del modelo certificada en `models/`.

## Criterios de Éxito
✅ **Zero-Binary Policy:** Git solo contiene código, documentación y configuraciones pequeñas.
✅ **Clean Diffs:** Los Notebooks no generan diffs ruidosos por metadatos o salidas de celdas.
✅ **Trazabilidad Fase-Rama:** Es posible saber a qué fase pertenece cada línea de código por el nombre de la rama de origen.

## Reglas Técnicas
- **Nbstripout:** El uso de herramientas de limpieza de notebooks es mandatorio antes de cualquier merge a `dev` o `main`.
- **Pre-commit Checks:** Todas las validaciones de esta habilidad deben reportarse como un checklist al usuario antes de proceder con el comando Git final.


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
