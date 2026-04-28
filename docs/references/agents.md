# agents.md — Directorio Maestro de Agentes de IA

Este documento centraliza el catálogo de agentes especializados disponibles en el proyecto. Cada agente tiene un rol único, un conjunto de habilidades (`skills`) y disparadores (`triggers`) que definen cuándo debe ser invocado.

> **Uso:** Los agentes residen en `.claude/agents/`. Las habilidades que invocan residen en `.claude/skills/`. Para invocarlo, menciona su nombre o uno de sus triggers en la conversación.

---

## 🏗️ Agentes de Gobernanza y Sesión

Agentes transversales responsables de la continuidad, integridad y gestión del ciclo de vida del proyecto.

| Agente                     | Cuándo usarlo                                                                                                                                                                                 | Skills                      |
| :------------------------- | :-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :-------------------------- |
| **ai-session-steward**     | Abrir o cerrar una sesión de trabajo. Redactar el [[handoff]] al final del día. Actualizar el log de decisiones. Preparar el briefing de tareas del día siguiente.                            | `session-management`        |
| **ai-repository-governor** | Auditar la higiene del repositorio. Crear estructura de carpetas inicial. Limpiar notebooks antes de commit. Gestionar PRs semánticos y ramas de Git.                                         | `repository-governance`     |
| **ai-change-manager**      | Detectar una desviación del [[SAD]], [[SpecDD]] o [[BRD]]. Formalizar un Control de Cambios (CC). Evaluar el impacto cascada de un cambio técnico. Actualizar la documentación de línea base. | `change-control-management` |
| **ai-backlog-manager**     | Crear o refinar el backlog. Descomponer un objetivo estratégico en tareas atómicas. Cambiar de fase o iteración. Auditar el progreso contra el DoD.                                           | `backlog-orchestration`     |
| **config-manager**         | Inicializar el proyecto (crear `config.md`). Registrar IDs de fuentes externas (NotebookLM, repositorios). Mantener las fuentes de verdad del proyecto actualizadas.                          | `project-config`            |

---

## 🔍 Agentes de Phase Discovery

Responsables de transformar una necesidad de negocio en una especificación técnica ejecutable.

| Agente                     | Cuándo usarlo                                                                                                                                                                                               | Skills                                                                                      |
| :------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------ |
| **ai-business-strategist** | Traducir el problema de negocio a KPIs técnicos. Escribir User Stories. Elaborar el [[BRD]]. Realizar análisis de costo-beneficio. Validar el ROI esperado. Redactar escenarios Gherkin (BDD) post-[[BRD]]. | `business-to-ml-translator`, `value-driven-product-mapper`, `gherkin-scenario-author`       |
| **ai-data-auditor**        | Inventariar fuentes de datos. Ejecutar el Gap Analysis. Generar el Data Feasibility Report. Analizar salud de datos (nulos, desbalance, ruido). Emitir veredicto GO/NO-GO.                                  | `diagnostic-data-auditor`, `feasibility-gap-analyzer`                                       |
| **ai-ux-designer**         | Diseñar el prototipo visual (mockup) tras la aprobación del reporte de factibilidad. Validar flujos de usuario y navegación. Gestionar el ciclo UAT con el Stakeholder.                                     | `ui-ux-prototyping`                                                                         |
| **ai-solutions-architect** | Diseñar el [[SAD]] (Software Architecture Document). Definir el [[SpecDD]] (interfaces `.py`). Diseñar el Contrato de Datos. Seleccionar el stack tecnológico. Definir la topología del sistema.            | `software-architecture-designer`, `[[specdd]]-interface-definer`, `data-contract-architect` |

---

## ⚙️ Agentes de Phase Engineering

Responsables de construir el pipeline de datos desde el dato crudo hasta el Feature Set certificado.

| Agente                         | Cuándo usarlo                                                                                                                                                                                               | Skills                                                                                                           |
| :----------------------------- | :---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :--------------------------------------------------------------------------------------------------------------- |
| **ai-data-engineer**           | Construir conectores e ingesta de datos. Implementar la capa Bronze. Ejecutar el EDA técnico post-ingesta. Gestionar seguridad de datos (cifrado, PII).                                                     | `multi-modal-data-extractor`, `bronze-layer-architect`, `technical-ingestion-profiler`, `data-security-governor` |
| **ai-analytics-engineer**      | Construir la capa Silver (limpieza y normalización). Ejecutar el EDA de transformación. Imputar valores nulos. Detectar sesgos introducidos por la limpieza.                                                | `silver-layer-architect`, `cleansing-bias-detector`, `data-imputation-specialist`                                |
| **ai-feature-store-architect** | Construir la capa Gold (feature engineering). Detectar Target Leakage y multicolinealidad. Implementar escaladores y encoders. Ejecutar el EDA estadístico.                                                 | `complex-feature-generator`, `statistical-gold-auditor`, `deterministic-preprocess-designer`                     |
| **ai-data-qa-engineer**        | Diseñar y ejecutar el ciclo TDD de datos. Validar esquemas con Pydantic / Great Expectations. Certificar cumplimiento del [[SAD]] y estándares técnicos. Traducir hallazgos del EDA en tests automatizados. | `data-test-case-designer`, `strict-schema-validator`, `technical-standard-certifier`                             |

---

## 🧠 Agentes de Phase Modeling

Responsables de entrenar, optimizar y certificar el modelo de Machine Learning.

| Agente                    | Cuándo usarlo                                                                                                                                                             | Skills                                                                                                                                 |
| :------------------------ | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :------------------------------------------------------------------------------------------------------------------------------------- |
| **ai-data-scientist**     | Seleccionar y evaluar algoritmos de ML. Optimizar hiperparámetros. Analizar importancia de variables. Entrenar el modelo baseline. Prototipar en notebooks.               | `algorithm-architecture-evaluator`, `hyperparameter-optimization-expert`, `feature-importance-analyzer`, `baseline-model-developer`    |
| **ai-ml-engineer**        | Industrializar el modelo (pasar de notebook a `.py`). Serializar el modelo (ONNX, Pickle, Joblib). Optimizar la latencia de inferencia. Refactorizar código experimental. | `model-industrialization-specialist`, `model-serialization-registry-manager`, `inference-latency-optimizer`, `code-refactoring-expert` |
| **ai-mlops-specialist**   | Configurar experiment tracking (MLflow, W&B). Gestionar el Model Registry. Validar el linaje datos→modelo. Provisionar infraestructura de entrenamiento.                  | `experiment-tracking-configurator`, `model-registry-governor`, `training-infrastructure-provisioner`, `data-model-lineage-validator`   |
| **ai-model-qa-validator** | Ejecutar benchmarking de rendimiento. Auditar sesgos y equidad algorítmica. Realizar stress testing del modelo. Certificar que el modelo supera los umbrales del [[BRD]]. | `model-performance-benchmarker`, `bias-fairness-auditor`, `model-robustness-stress-tester`                                             |

---

## 🚀 Agentes de Phase Delivery

Responsables de empaquetar el modelo como un producto de software y desplegarlo.

| Agente                       | Cuándo usarlo                                                                                                                                                                          | Skills                                                                                             |
| :--------------------------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | :------------------------------------------------------------------------------------------------- |
| **ai-backend-engineer**      | Construir la API de inferencia (FastAPI). Integrar módulos de limpieza e inferencia. Gestionar concurrencia y asincronía. Implementar validación de contratos de entrada ([[SpecDD]]). | `high-performance-api-builder`, `concurrency-async-orchestrator`, `input-contract-enforcer`        |
| **ai-frontend-engineer**     | Construir el dashboard interactivo (Streamlit / React). Visualizar explicabilidad XAI (SHAP). Gestionar el flujo de feedback del usuario.                                              | `interactive-dashboard-builder`, `xai-visualizer-specialist`, `ux-feedback-loop-designer`          |
| **ai-mlops-cloud-architect** | Contenedorizar la aplicación (Docker). Diseñar el pipeline CI/CD. Configurar auto-escalado en la nube. Optimizar costos operativos.                                                    | `full-stack-containerizer`, `ci-cd-pipeline-automated-designer`, `cloud-scalability-cost-optimzer` |
| **ai-full-stack-sdet**       | Ejecutar pruebas E2E del sistema completo. Realizar load & stress testing. Auditar seguridad y privacidad de datos. Certificar la integración total antes del despliegue.              | `e2e-integration-tester`, `system-load-stress-tester`, `security-vulnerability-auditor`            |

---

## 🗺️ Mapa de Agentes por Fase

```
Phase Discovery      Phase Engineering    Phase Modeling       Phase Delivery
─────────────────    ──────────────────   ──────────────────   ──────────────────
ai-business-         ai-data-engineer     ai-data-scientist    ai-backend-engineer
  strategist         ai-analytics-        ai-ml-engineer       ai-frontend-engineer
ai-data-auditor        engineer           ai-mlops-specialist  ai-mlops-cloud-
ai-ux-designer       ai-feature-store-    ai-model-qa-           architect
ai-solutions-          architect            validator          ai-full-stack-sdet
  architect          ai-data-qa-engineer

Transversales (todas las fases)
────────────────────────────────────────────────────────────────────────────────
ai-session-steward · ai-repository-governor · ai-change-manager
ai-backlog-manager · config-manager
```

---

> **Nota:** Para adoptar plenamente el rol y las restricciones de un agente, revisar su archivo individual en `.claude/agents/<nombre>.md` antes de ejecutar una tarea compleja.
