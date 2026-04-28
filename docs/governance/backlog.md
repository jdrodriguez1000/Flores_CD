# Backlog del Proyecto: Flores_CD

Este documento es la única fuente de verdad para la orquestación del proyecto. Sigue la jerarquía **Fase > Iteración > Tarea** y los principios de la metodología **SpecDD + TDD**.

## 📊 Estado Global
- **Fase Actual:** Phase Discovery
- **Iteración Actual:** Iteración 0.0 (Setup y Gobernanza)
- **Progreso de Iteración:** 40%

---

## 🚦 Leyenda de Estados
- 🟢 **Completada**: Tarea finalizada y validad.
- 🟡 **En progreso**: Tarea con trabajo activo.
- ⚪ **No iniciada**: Tarea pendiente en el backlog.
- 🔴 **Bloqueada**: Tarea detenida por dependencias o fallos.

---

## 🗺️ Mapa de Fases
1.  **Phase Discovery (Discovery & Business Understanding)** 🟢 *En curso*
2.  **Phase Engineering (Data Ingestion & Quality)** ⚪ *Pendiente*
3.  **Phase Modeling (Model Development)** ⚪ *Pendiente*
4.  **Phase Delivery (Software Integration)** ⚪ *Pendiente*

---

## 0. Phase Discovery

### Iteración 0.0: Setup y Gobernanza Inicial
- **Estado:** 🟡 En progreso
- **Objetivo:** Establecer los cimientos del proyecto, la estructura de carpetas y los documentos maestros de gobernanza.

| ID | Tarea | Agente Responsable | Estado | DoD (Definition of Done) |
| :--- | :--- | :--- | :--- | :--- |
| **T0.1** | Inicialización de Repositorio y Estructura | `ai-repository-governor` | 🟢 **Completada** | Estructura según `directory.md` y `.gitkeep` creados. |
| **T0.2** | Configuración de Git Flow y Ramas Base | `ai-repository-governor` | 🟢 **Completada** | Ramas `main` y `dev` sincronizadas en GitHub. |
| **T0.3** | Creación y Estructuración del Backlog | `ai-backlog-manager` | 🟢 **Completada** | `backlog.md` actualizado con tareas de la Iteración 0. |
| **T0.4** | Protocolo Ask-Me: Entendimiento Compartido | `ai-business-strategist` | 🟢 **Completada** | `shared_understanding.md` completado y firmado. |
| **T0.5** | Configuración de Identidad del Proyecto | `config-manager` | ⚪ **No iniciada** | `config.md` con IDs y metadatos oficiales. |
| **T0.6** | Redacción del BRD (Requerimientos) | `ai-business-strategist` | ⚪ **No iniciada** | `BRD.md` aprobado con KPIs y objetivos claros. |
| **T0.7** | Definición de Contrato Behavior (BDD) | `ai-business-strategist` | ⚪ **No iniciada** | `behavior.md` con escenarios Gherkin Given/When/Then. |
| **T0.8** | Reporte de Factibilidad de Datos | `ai-data-auditor` | ⚪ **No iniciada** | `feasibility.md` con inventario y análisis de gaps de datos. |
| **T0.9** | Construcción del Mockup Visual (Prototipo) | `ai-ux-designer` | ⚪ **No iniciada** | `mockup/index.html` funcional (smoke & mirrors) + `mockup.md`. |
| **T0.10** | Diseño de Arquitectura de Software (SAD) | `ai-solutions-architect` | ⚪ **No iniciada** | `SAD.md` con stack técnico y diagramas C4. |
| **T0.11** | Especificación de Interfaces (SpecDD) | `ai-solutions-architect` | ⚪ **No iniciada** | `SpecDD.md` con firmas de funciones y contratos. |
| **T0.12** | Creación del Contrato de Datos | `ai-solutions-architect` | ⚪ **No iniciada** | `contract.md` con esquemas y validaciones rígidas. |

---

## 📝 Notas de Gestión
- **DoD Absoluto:** Ninguna tarea técnica se considera completada sin su respectivo test en `GREEN`.
- **UAT:** La iteración 0.0 se considerará finalizada tras la aprobación humana de todos los documentos de gobernanza iniciales.
