# Project Documentation Registry

Este documento detalla los archivos de gobernanza, soporte y técnicos que componen la base de conocimiento y el control del proyecto. Es la referencia para entender la ubicación, propósito y jerarquía documental.

## 1. Documentos de Gobernanza (Estratégicos)

Ubicados principalmente en `docs/governance/`, definen las reglas de negocio y los contratos técnicos.

| Documento    | Ubicación                     | Propósito                                                    |
| :----------- | :---------------------------- | :----------------------------------------------------------- |
| **BRD**      | `docs/governance/BRD.md`      | Business Requirements Document: Objetivos de negocio y KPIs. |
| **SAD**      | `docs/governance/SAD.md`      | Software Architecture Document: Stack tecnológico y diseño.  |
| **BACKLOG**  | `docs/governance/backlog.md`  | Orquestación de tareas (Fases > Iteraciones > Tareas).       |
| **SpecDD**   | `docs/governance/SpecDD.md`   | Especificación de Interfaces: Contratos y firmas de código.  |
| **BEHAVIOR** | `docs/governance/behavior.md` | Contrato BDD: Escenarios Gherkin para validación E2E.        |
| **CONTRACT** | `docs/governance/contract.md` | Contrato de Datos: Validaciones matemáticas de variables.    |

## 2. Documentos de Soporte y Operación

Documentos vivos que registran la evolución diaria y las decisiones críticas.

| Documento        | Ubicación                       | Propósito                                                    |
| :--------------- | :------------------------------ | :----------------------------------------------------------- |
| **HANDOFF**      | `docs/references/handoff.md`    | Estado operativo diario, pendientes y bloqueos.              |
| **DECISIONS**    | `docs/references/decisions.md`  | Log histórico de decisiones técnicas y lecciones aprendidas. |
| **CHANGES (CC)** | `docs/changes/`                 | Fichas de Control de Cambios (una por CC-ID).                |
| **DIRECTORY**    | `docs/references/directory.md`  | Guía de estructura de carpetas y archivos.                   |
| **PRINCIPLES**   | `docs/references/principles.md` | Principios de ingeniería y desarrollo del proyecto.          |

## 3. Entregables Técnicos por Fase

Documentos generados como resultado de la ejecución de cada etapa del proyecto.

- **Phase Discovery (`docs/Phase_discovery/`):**
    - `feasibility.md`: Diagnóstico de salud de datos y brechas de factibilidad.
    - `shared_understanding.md`: Log de alineación entre negocio y técnica.
- **Phase Engineering (`docs/Phase_engineering/`):**
    - `EDA_ingestion.md`: Reporte de calidad post-ingesta (Bronze).
    - `EDA_cleaning.md`: Reporte de limpieza y normalización (Silver).
- **Phase Modeling (`docs/Phase_modeling/`):**
    - `model_qa.md`: Benchmarking, métricas de rendimiento y análisis de sesgo.
- **Phase Delivery (`docs/Phase_delivery/`):**
    - `qa_system.md`: Reporte final de integración E2E y Stress Testing.


## Directiva de Actualización
- Ningún documento de la Sección 1 puede modificarse sin un **Control de Cambios (CC)**.
- Los documentos de la Sección 2 son de actualización frecuente por los agentes de IA (especialmente `handoff.md`).
- Los documentos de la Sección 3 son inmutables una vez que la fase correspondiente ha sido certificada.
