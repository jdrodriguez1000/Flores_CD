---
name: backlog-orchestration
description: Protocolo para la gestión jerárquica del proyecto, atomización de especificaciones técnicas y aseguramiento del DoD.
user-invocable: false
agent: ai-backlog-manager
allowed-tools: [Read, Write, Edit, Bash]
---

# Skill: Orquestación de Backlog

Esta habilidad permite transformar el plan maestro en acciones ejecutables, asegurando la trazabilidad total del proyecto.

## Funciones

### 1. Inicialización de Roadmap (Slices Verticales)
**Acción:** `initialize_roadmap`
- Crea el archivo `docs/governance/backlog.md`.
- Estructura el Backlog usando un enfoque iterativo de **Slices Verticales (Balas Trazadoras)**.
- **Estructura Obligatoria de Iteración:**
    *   **Título de Iteración**
    *   **Estado:** [No iniciada | En progreso | Finalizada | Bloqueada]
    *   **Objetivo:** Descripción técnica del hito.
    *   **Criterio de Éxito de Usuario Final (UAT):** Descripción narrativa de lo que el usuario final verá, experimentará o podrá realizar al finalizar la iteración.
    *   **Fuera de Alcance de la Iteración:** Lista explícita de funcionalidades o componentes que NO se entregarán ni se deben esperar en esta iteración específica para evitar el scope creep.
- **Prohibición de Cascada:** El Backlog NUNCA debe estructurarse exigiendo terminar el 100% de la capa de datos antes del modelo.

### 2. Atomización de SpecDD (TDD Mapping)
**Acción:** `atomize_specs_to_tasks`
- Escanea el `SpecDD`, el `Data Contract` y, obligatoriamente, el `docs/governance/behavior.md` (BDD).
- **Regla Mandatoria RED/GREEN:** Por cada componente definido, genera obligatoriamente dos ítems vinculados en el Backlog, **anclados a un escenario BDD**:
    1.  **Tarea [RED]:** Desarrollo de la Suite de Pruebas. El DoD es el fallo controlado del test asociado al escenario BDD.
    2.  **Tarea [GREEN]:** Implementación de la lógica productiva. El DoD es que el test asociado pase exitosamente a `GREEN`.
- Asigna el agente responsable (ej: `ai-data-engineer` para la lógica y `ai-data-sdet` para el test).

### 3. Gestión de Tareas e Iteraciones (Management Protocol)
**Acción:** `manage_backlog_state`

#### Estados de Tareas:
- `No iniciada`
- `En progreso`
- `Completada`

#### Estados de Iteraciones:
- `No iniciada`
- `En progreso`
- `Finalizada`
- `Bloqueada`

#### Protocolo de Cierre de Iteración:
1.  **Condición de Completitud:** Todas las tareas de la iteración deben estar en estado `Completada`.
2.  **Validación Humana Obligatoria:** Una iteración solo pasa a `Finalizada` tras la confirmación explícita del humano de que es realmente funcional después de realizar sus propias pruebas como usuario final.
3.  **Gestión de Fallos (Bloqueo):** Si todas las tareas están `Completada` pero las pruebas del humano fallan, la iteración debe marcarse como `Bloqueada`.
4.  **Recuperación de Bloqueo:** Es obligatorio agregar nuevas tareas de tipo `Revision / Fallo Test` para resolver los problemas presentados. No se permite continuar con la siguiente iteración sin estas tareas.

### 4. Planificación de Pruebas de Usuario (UAT Planning)
**Acción:** `generate_uat_plan`
- Inmediatamente después de definir las tareas de la iteración, el agente debe generar un archivo en `tests/UAT/uat_iteracion_X.md`.
- **Contenido Obligatorio del Plan UAT:**
    *   **Nombre de la Prueba:** Identificador claro (ej: UAT-01: Clasificación Setosa).
    *   **Paso a Paso:** Instrucciones detalladas para que el humano ejecute la prueba.
    *   **Valor Esperado:** Qué debe suceder para que la prueba se considere exitosa.
    *   **Tipos de Escenarios:** Debe incluir obligatoriamente pruebas para el **Camino Feliz (Happy Path)** y para los **Caminos No Felices / Alternativos**.
- **Prohibición de Ejecución por IA:** Queda terminantemente prohibido que cualquier agente de IA utilice herramientas (como Playwright, Selenium o Bash) para ejecutar estas pruebas. El plan UAT es un documento de guía exclusivo para el humano.

## Estructura de Tarea en backlog.md
```markdown
### [ID] Título de la Tarea
- **Responsable:** @agente-nombre
- **Iteración:** X.Y
- **Entregable:** Nombre del Archivo/Componente
- **Acción:** [Coding | Testing | Documentation | Revision / Fallo Test]
- **BDD Target:** [ID del Escenario en behavior.md]
- **DoD:** Tests E2E/Unitarios para el escenario target arrojan GREEN.
- **Estado:** [No iniciada | En progreso | Completada]
```

## Reglas de Validación
- **Prohibido la multifunción:** Si una tarea dice "Desarrollar y Probar", debe ser rechazada y separada en dos tareas independientes.
- **Trazabilidad Absoluta al BDD:** Toda tarea técnica debe citar el escenario exacto del `behavior.md` (BDD). Si una tarea no puede ser mapeada a un escenario BDD, debe ser devuelta al Estratega de Negocios por representar comportamiento no documentado ni acordado.


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
