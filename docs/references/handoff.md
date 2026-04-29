# handoff.md — Estado Operativo del Proyecto

> Documento de continuidad. Un agente nuevo debe poder retomar el trabajo leyendo solo este archivo.

- **Fecha de cierre:** 2026-04-29
- **Rama activa:** `slice/F0-backlog-init`
- **Fase:** Phase Discovery
- **Iteracion:** 0.0 — Setup y Gobernanza Inicial
- **Progreso de iteracion:** 90% (BRD v1.8.0 cerrado; behavior.md v1.5.0 certificado con CC-005; T0.7 finalizada sin vacíos bloqueantes)

---

## Logros de la sesion (actualizacion 2026-04-29)

| Tarea | Entregable | Estado |
| :--- | :--- | :--- |
| **Agentes actualizados (x6)** | `.claude/agents/ai-data-scientist.md` — Nuevos triggers, mision operativa reescrita, 3 nuevas Reglas de Oro | Completada |
| **Skill actualizado** | `.claude/skills/algorithm-architecture-evaluator/SKILL.md` — Filtro de Eficiencia, Round Robin con CV std, Seleccion Dual con artefacto YAML | Completada |
| **Skill actualizado** | `.claude/skills/baseline-model-developer/SKILL.md` — Baseline como Modelo Control, Challenger como Tratamiento, preparacion del Shadow Test | Completada |
| **Skill actualizado** | `.claude/skills/hyperparameter-optimization-expert/SKILL.md` — Optimizacion diferenciada por rol (Control vs. Tratamiento), estudios Optuna separados | Completada |
| **Skill actualizado** | `.claude/skills/feature-importance-analyzer/SKILL.md` — SHAP y Permutation Importance aplicados a ambos modelos, tabla comparativa, alerta de divergencia SHAP | Completada |
| **Agente actualizado** | `.claude/agents/ai-business-strategist.md` — Hard Rule #6 Shadow Test Intent incorporada | Completada |
| **BRD v1.6.0** | `docs/governance/BRD.md` — 7 micro-decisiones post-auditoria aplicadas; esquema SQLite expandido a 21 campos (`prediction_batch_id`); KPI-T-05 simplificado; dominio `estado` completo; RF-08 con distincion de modos de fallo | Completada |
| **BRD v1.7.0** | `docs/governance/BRD.md` — segunda auditoria devil's advocate; 5 micro-decisiones (D-026 a D-030): filtrado tecnico de cola, label UI A2, flujo post-accion A2, prediction_batch_id sin par, CA-03/CA-04 con valores concretos | Completada |
| **BRD v1.8.0** | `docs/governance/BRD.md` — tercera auditoria devil's advocate (perspectiva implementador Gherkin); 2 micro-decisiones (D-031, D-032): mock canonico CA-02a con mapping de clases, tabla de valores de especie_analista1 por camino del A1, CA-03 con decision del A1 fijada | Completada |
| **BRD v1.9.0** | `docs/governance/BRD.md` — CC-006: estado "Anulada", atomicidad en SQLite, True Positive en KPI-T-05 | Completada |
| **behavior.md** | `docs/governance/behavior.md` — Contrato BDD redactado con 14 escenarios Gherkin; cubre US-01 a US-03, RF-08/RF-08b (Resiliencia Control/Shadow) y RF-01a (Session State). **Certificado con CC-006 (v1.6.0)**. | Completada |

### Detalle de la sesion

**Contrato BDD y Auditoría DoD (behavior.md v1.5.0):**
- Se han definido 14 escenarios Gherkin determinísticos que traducen el BRD v1.8.0 a especificaciones ejecutables.
- **Auditoría Final Devil's Advocate:** Se inyectaron escenarios críticos de "borde lógico": Rechazo+Baja Confianza, Labels Dinámicos A2, Inputs Vacíos, Zero State y Tratamiento No Configurado.
- **Blindaje Devil's Advocate (CC-005):** Se agregaron escenarios de resiliencia para fallos del Modelo Tratamiento y Control (RF-08/RF-08b), validación estricta de rangos de input (RF-01b), exclusión de auto-casos, persistencia de timestamps y mutabilidad cero estricta.
- **Matriz de DoD Integral:** Se incorporó una matriz de 14 criterios que vincula cada funcionalidad con su criterio de aceptación de negocio y su método de verificación técnica (Unit/E2E/Auditoría DB), asegurando que "todo tenga su DoD".
- Cobertura completa de la máquina de estados: 4 caminos para el Analista 1, 3 acciones para el Analista 2 con navegación persistente y manejo de Zero State en la cola.

---

## Pendientes (proxima sesion)

| ID | Tarea | Agente Responsable | Dependencia |
| :--- | :--- | :--- | :--- |
| **T0.8** | Reporte de Factibilidad de Datos | `ai-data-auditor` | T0.4 resuelta |
| **T0.9** | Construccion del Mockup Visual | `ai-ux-designer` | BRD v1.8.0 |
| **T0.10** | Diseno de Arquitectura de Software (SAD) | `ai-solutions-architect` | T0.6 resuelta, T0.7 completada (v1.5.0 certified) |
| **T0.11** | Especificacion de Interfaces (SpecDD) | `ai-solutions-architect` | T0.10 |
| **T0.12** | Creacion del Contrato de Datos | `ai-solutions-architect` | T0.10 |

**Tarea #1 para la proxima sesion:** T0.8 — Reporte de Factibilidad de Datos por el `ai-data-auditor`.

---

## Bloqueadores activos

Ninguno. El BRD v1.8.0 y el behavior.md v1.5.0 están aprobados y certificados. El escuadrón de agentes está alineado.

---

## Contexto critico para retomar

1. **BRD v1.8.0** (`docs/governance/BRD.md`) y **behavior.md v1.5.0** son las fuentes de verdad vigentes.
2. **Matriz de DoD**: Consultar la sección final de `behavior.md` para los 14 criterios de éxito técnicos de cada slice vertical.
3. **Resiliencia Shadow y Control**: El fallo del modelo tratamiento DEBE ser silencioso. El fallo del modelo control DEBE interceptarse y mostrar un mensaje controlado (RF-08b).
4. **Navegación A2**: Tras confirmar/corregir, el analista debe permanecer en la vista de cola (si hay más casos) o ver el "Zero State" si la cola queda vacía.
5. **Aislamiento de Sesión**: `analista_id` es obligatorio y debe limpiarse en cada recarga de página (RF-01a).
6. **Timestamps**: Todos los registros deben persistir el momento exacto de la operación para habilitar KPIs de negocio.

---

## Estado del repositorio

- Rama activa: `slice/F0-backlog-init`
- Archivos modificados/creados en esta sesion:
  - `.claude/agents/ai-data-scientist.md` — MODIFICADO (Torneo, Efficiency Gate, Reglas de Oro nuevas)
  - `.claude/skills/algorithm-architecture-evaluator/SKILL.md` — MODIFICADO (Secciones II-IV reescritas)
  - `.claude/skills/baseline-model-developer/SKILL.md` — MODIFICADO (Control/Tratamiento/Shadow Test)
  - `.claude/skills/hyperparameter-optimization-expert/SKILL.md` — MODIFICADO (optimizacion diferenciada por rol)
  - `.claude/skills/feature-importance-analyzer/SKILL.md` — MODIFICADO (SHAP dual model, alerta divergencia)
  - `.claude/agents/ai-business-strategist.md` — MODIFICADO (Hard Rule #6 Shadow Test Intent)
  - `docs/governance/BRD.md` — ACTUALIZADO (v1.5.0 → v1.6.0 → v1.7.0 → v1.8.0, 14 micro-decisiones acumuladas)
  - `docs/governance/behavior.md` — ACTUALIZADO (v1.4.0 → v1.5.0, aplicación de CC-005)
  - `docs/changes/CC-005.md` — CREADO (Ficha de control de cambios)
  - `docs/references/decisions.md` — ACTUALIZADO (D-026 a D-048 registrados)
  - `docs/references/handoff.md` — ACTUALIZADO (este archivo)
- Pendiente de commit y merge a rama de iteracion: a cargo del `ai-repository-governor`