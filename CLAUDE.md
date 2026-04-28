# CLAUDE.md: Protocolos de Ingeniería de IA y Ciencia de Datos

Este archivo define las convenciones y protocolos agnósticos para Gemini CLI en proyectos de **Ciencia de Datos, Machine Learning e Ingeniería de IA**, siguiendo las metodologías **SpecDD** (Specification-Driven Development) y **TDD** (Test-Driven Development).


## Rituales de Sesión

### Ritual de Apertura (Session Kickoff)
1. **Contexto y Alineación Obligatoria:**
   - **LECTURA OBLIGATORIA:** Leer y comprender el archivo **[principles.md](docs/references/principles.md)**. Todas tus acciones, sugerencias y generación de código deben cumplir estrictamente con los principios definidos en este archivo.
   - **Leer** `handoff.md`, `decisions.md` y `backlog.md` para reconstruir el contexto completo de la sesión anterior.
2.  **Priorización:** Seleccionar la siguiente tarea atómica pendiente del Backlog.

> **Nota:** NotebookLM no se consulta en apertura. Es una base de conocimiento profundo que se consulta **a demanda**, cuando los documentos operativos no son suficientes para responder una pregunta específica.

### Ritual de Cierre (Session Wrap-up)
1.  **Validación:** Asegurar que todos los tests estén en verde antes de cerrar.
2.  **Documentación (`ai-session-steward`):** Actualizar `handoff.md` (Logros, Pendientes, Bloqueos) y registrar en `decisions.md` (Decisiones, Lecciones aprendidas).
3.  **Versionado (`ai-repository-governor`):** Hacer commit semántico y subir los cambios al repositorio actual.
4.  **Sincronización del Cerebro (`notebooklm`):** Llevar al cerebro del proyecto en NotebookLM: `[[handoff]].md`, `decisions.md` y las fichas nuevas o modificadas en `docs/changes/`.


## Directrices de Comportamiento
* **PROTOCOLO DE PENSAMIENTO:** Antes de realizar cualquier cambio (usando `write_file` o `edit_file`), debes validar internamente que tu solución respeta los pilares de "Simplicidad Primero" y "Cambios Quirúrgicos" detallados en `principles.md`.
* **AUDITORÍA:** Si detectas que una instrucción del usuario contradice los principios (por ejemplo, pide sobre-ingeniería innecesaria), debes advertirlo antes de proceder.
* **VERIFICACIÓN:** Al finalizar una tarea, confirma brevemente que la solución es la mínima necesaria para resolver el problema, evitando abstracciones prematuras.


## Directivas Fundamentales

### Mentalidad de Auditor (Devil's Advocate)
Cuestiona proactivamente la factibilidad de los datos, la lógica de los KPIs y la arquitectura del modelo. **Rechaza tareas sin criterios de aceptación técnicos (Thresholds) definidos.** 

### Soberanía Documental (SpecDD + BDD)
El código productivo (`.py`) es un reflejo estricto de la especificación técnica. **Cada línea de código debe ser trazable hacia el BRD (Ver detalle de la jerarquía en la Sección 4).** Se prohíbe la improvisación de lógica de limpieza o modelado fuera del flujo documentado.

### Separación de Entornos (Notebook vs. Production)
*   **Notebooks (`notebooks/`):** Espacio de R&D, descubrimiento y descarte de hipótesis algorítmicas. No requieren código "grado producción" pero deben ser legibles y estar documentados.
*   **Módulos (`src/`):** Código industrializado, modular, con tipado estricto y siguiendo los contratos del SpecDD. **Solo el código en `src/` entra en el pipeline de despliegue.**

### Cadena de Confianza y Linaje
Toda transformación de datos y entrenamiento de modelos debe ser reproducible. Se debe mantener el linaje: **VERSIÓN DE DATOS (Gold) → VERSIÓN DE CÓDIGO (src) → VERSIÓN DE MODELO (models).**

### Higiene del Entorno Técnico
*   **Aislamiento:** Todo desarrollo debe realizarse dentro de un **Ambiente Virtual** (`venv` o `conda`).
*   **Dependencias:** El archivo **`requirements.txt`** es la única fuente de verdad para librerías. Debe actualizarse inmediatamente al instalar nuevas dependencias.
*   **Portabilidad:** Se prohíbe el uso de rutas absolutas. Todo enlace en documentos y toda referencia en el código debe utilizar **rutas relativas** respecto a la raíz del proyecto para garantizar la movilidad total del repositorio.


## Configuracion del proyecto y metodologia de trabajo

Para configuración específica del proyecto actual (nombre, stack, IDs), consulta siempre **[docs/references/config.md](docs/references/config.md)**.

Seguir la metodologia de trabajo para proyectos de ciencia de datos y machine learning, qeu se encuentra en el archivo **[process.md](docs/methodology/process.md)**.


## 🌐 Fuentes de Verdad Vivas (Live Context)

Para garantizar la paridad técnica con las versiones más recientes y eliminar el "vibecoding", consulta las URLs documentadas en **[docs/references/sources.md](docs/references/sources.md)**.

**Directiva de Uso:** Accede y procesa estas URLs **ÚNICAMENTE** a demanada, cuando la tarea implique escribir o modificar código relacionado con las tecnologías allí listadas. Si detectas discrepancias entre tu memoria interna y el contenido de estas URLs, la URL siempre prevalece como Fuente de Verdad.


## Estructura de directorios del proyecto

Para garantizar la organización y trazabilidad, se sigue la jerarquía definida en el documento maestro de estructura. **Consulta obligatoria: [directory.md](docs/references/directory.md)**.


## Documentos del proyecto

Para garantizar la soberanía documental y el control de versiones, se mantiene un registro centralizado de todos los activos de conocimiento. **Consulta obligatoria: [documents.md](docs/references/documents.md)**.


## Ciclo de Desarrollo: SpecDD + BDD + TDD

La metodología opera en tres capas de especificación que se complementan:

| Capa       | Documento                     | Pregunta que responde                                       |
| :--------- | :---------------------------- | :---------------------------------------------------------- |
| **SpecDD** | `docs/governance/SpecDD.md`   | ¿Qué hace cada función? (contratos e interfaces)            |
| **BDD**    | `docs/governance/behavior.md` | ¿Cómo se comporta el sistema con ejemplos reales? (Gherkin) |
| **TDD**    | `tests/`                      | ¿El código es correcto para cumplir los dos anteriores?     |

Todo desarrollo sigue el ciclo **Red-Green-Refactor-Certificación-Validación**:

1.  **RED (Test Fallido):** El `ai-data-qa-engineer` escribe el test basado en el escenario Gherkin del `behavior.md` y el contrato del `SpecDD`. El test falla porque el módulo no existe aún.
2.  **GREEN (Funcionalidad):** Escribir código mínimo para pasar el test.
3.  **REFACTOR (Calidad):** Optimización técnica y cumplimiento del linaje.
4.  **CERTIFICACIÓN (Técnica):** Validación contra [[SAD]] & SpecDD.
5.  **VALIDACIÓN (Negocio):** Verificación final contra el BRD, behavior.md y KPIs.


## Protocolo de Control de Cambios (CC)

Obligatorio cuando se detecta una desviación de los documentos de gobernanza. Se ejecuta a través del agente **`ai-change-manager`**.

### Paso 1 — `evaluate_drift` (Evaluación de Deriva)
Parada inmediata del código. Comparar la intención del cambio contra el **[[SAD]]** y el **SpecDD**. Identificar qué secciones exactas de la documentación quedarían obsoletas.

### Paso 2 — `generate_cc_proposal` (Ficha de CC)
Generar la propuesta en el chat con los siguientes campos:
- **CC-ID:** Identificador único correlativo.
- **Cambio:** Descripción técnica clara de lo que se modifica.
- **Justificación:** Por qué es necesario o mejor el cambio.
- **Impacto:** Lista de documentos afectados con sección exacta.

### Paso 3 — Autorización
Esperar **"APROBADO"** explícito del usuario. Sin esta confirmación, no se ejecuta ningún cambio.

### Paso 4 — `execute_approved_change` (Efecto Cascada)
1. Actualizar los archivos de gobernanza (`.md`) afectados.
2. Crear la ficha formal en **`docs/changes/CC-<ID>.md`**.
3. Registrar una referencia al CC-ID en **`docs/references/decisions.md`**.
4. Emitir un **Token de Continuidad** al agente original para que retome el trabajo con la nueva especificación vigente.

### Reglas Técnicas
- **Atomicidad:** Un CC trata un único cambio o grupo de cambios altamente relacionados. No mezclar cambios de negocio con refactores técnicos.
- **Rollback Mental:** Si el usuario rechaza el CC, sugerir la alternativa más cercana a la documentación original sin romper el sistema.


## Protocolo de Gestión de Versiones (Git)

### Soberanía de Ramas (Jerarquía Mandatoria)
El repositorio opera bajo una estructura de 4 niveles de aislamiento:
1.  **`main`**: Rama de producción. Solo recibe merges de `dev` tras certificación final de fase. protegida.
2.  **`dev`**: Rama de integración. Recibe merges de las ramas de `iteracion`. protegida.
3.  **`iteracion/X.Y`**: Rama de estabilización de la iteración. (Ej: `iteracion/2.1`).
4.  **`slice/F[1-4]-<nombre>`**: Ramas de trabajo atómico por Slice Vertical. (Ej: `slice/F2-ingesta-bronze`). Todo desarrollo ocurre AQUÍ.

### Flujo de Trabajo (Git Flow)
- Todo desarrollo inicia en una rama **`slice`**.
- Al completar un Slice (DoD GREEN), se hace merge a la rama de **`iteracion`** activa.
- Al finalizar la iteración (UAT Aprobada), se hace merge a **`dev`**.

### Commits Semánticos (Foco DS/ML)
*   `feat(data):` Transformaciones en Bronze/Silver/Gold.
*   `feat(model):` Ajustes en entrenamiento o arquitectura.
*   `test(qa):` Validaciones de datos y tests unitarios.
*   `docs(f-X):` Cambios en documentación de la fase X.



## Escuadrón de Agentes Especializados

El proyecto opera con un escuadrón de agentes especializados por fase y rol. El catálogo completo — incluyendo triggers, skills asignados y cuándo invocar cada agente — se encuentra en **[agents.md](docs/references/agents.md)**.


