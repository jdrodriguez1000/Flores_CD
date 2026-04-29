# handoff.md — Estado Operativo del Proyecto

> Documento de continuidad. Un agente nuevo debe poder retomar el trabajo leyendo solo este archivo.

- **Fecha de cierre:** 2026-04-29
- **Rama activa:** `slice/F0-backlog-init`
- **Fase:** Phase Discovery
- **Iteracion:** 0.0 — Setup y Gobernanza Inicial
- **Progreso de iteracion:** 85% (BRD v1.8.0 cerrado — tercera auditoria devil's advocate completada, 3 micro-decisiones resueltas; T0.7 habilitada sin vacios bloqueantes)

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
| **behavior.md** | `docs/governance/behavior.md` — Contrato BDD redactado con 8 escenarios Gherkin; cubre US-01 a US-03, RF-08 (Shadow Testing) y RF-01a (Session State) | Completada |

### Detalle de la sesion

**Contrato BDD (behavior.md):**
- Se han definido 8 escenarios Gherkin determinísticos que traducen el BRD v1.8.0 a especificaciones ejecutables.
- Cobertura completa de la máquina de estados: 4 caminos para el Analista 1, 3 acciones para el Analista 2.
- Escenario específico para Shadow Testing que verifica la creación del par de registros (control/shadow) con el mismo `prediction_batch_id`.
- Escenario para RF-01a que garantiza el aislamiento del `analista_id` entre recargas de página, crítico para el despliegue en PC compartida.
- Uso de mock canónico (D-031) para el test de CA-02a (baja confianza automática).

---

## Pendientes (proxima sesion)

| ID | Tarea | Agente Responsable | Dependencia |
| :--- | :--- | :--- | :--- |
| **T0.8** | Reporte de Factibilidad de Datos | `ai-data-auditor` | T0.4 resuelta |
| **T0.9** | Construccion del Mockup Visual | `ai-ux-designer` | BRD v1.8.0 |
| **T0.10** | Diseno de Arquitectura de Software (SAD) | `ai-solutions-architect` | T0.6 resuelta, T0.7 completada |
| **T0.11** | Especificacion de Interfaces (SpecDD) | `ai-solutions-architect` | T0.10 |
| **T0.12** | Creacion del Contrato de Datos | `ai-solutions-architect` | T0.10 |

**Tarea #1 para la proxima sesion:** T0.8 — Reporte de Factibilidad de Datos por el `ai-data-auditor`.

---

## Bloqueadores activos

Ninguno. El BRD v1.5.0 esta aprobado y el escuadron de agentes esta alineado con el paradigma de Torneo + Shadow Testing.

---

## Contexto critico para retomar

1. **BRD v1.8.0** (`docs/governance/BRD.md`) es la unica fuente de verdad vigente. Versiones anteriores quedan obsoletas.
2. **Maquina de estados completa (5 transiciones):**
   - Alta confianza + A1 acepta → `confirmada_a1` (caso cerrado, sin Analista 2)
   - Baja confianza + cualquier decision A1 → `pendiente`
   - Alta confianza + A1 rechaza → `pendiente`
   - Analista 2 confirma → `confirmada`
   - Analista 2 corrige → `corregida`
3. **Shadow testing completamente invisible para los analistas.** Ambos modelos ejecutan en paralelo; solo el control es visible. El tratamiento tiene estado fijo `shadow`.
4. **Esquema SQLite: 21 campos.** Los 3 campos de CC-003 (`decision_analista1`, `especie_analista1`, `timestamp_decision_analista1`) son nulos para registros shadow. El campo `prediction_batch_id` (UUID) es compartido entre el registro control y el shadow del mismo ciclo — es el mecanismo de join para KPI-T-05.
5. **KPI-T-05:** Ground truth construido desde el veredicto final del flujo operativo, no desde etiquetas externas.
6. **behavior.md debe cubrir 4 caminos para el mecanismo A1** ademas de escenarios de shadow testing. CA-02a y CA-02b del BRD siguen vigentes.
7. **CA-02 dividida en CA-02a y CA-02b.** CA-02a usa mock de modelo con prob=[0.45, 0.30, 0.25] → activa advertencia de baja confianza. CA-02b es el test con input real post-entrenamiento.
8. **Umbral de baja confianza 0.60:** calibrable post-entrenamiento. Criterio: ≤15% de predicciones del test set de Iris deben activar la advertencia.
9. **Modelo Control = estabilidad (minimo CV std). Modelo Tratamiento = maxima precision.** El Efficiency Gate se aplica a todos los candidatos antes de evaluar precision.
10. **El ai-business-strategist es el punto de captura de la intencion de Shadow Testing (Fase 0)**, no el ai-data-scientist. La Hard Rule #6 lo formaliza.
11. El `config.md` es la fuente de verdad para IDs externos (NotebookLM ID: `7169f5cf-1c59-43ea-aa59-56d5e9f1dff3`, GitHub: `https://github.com/jdrodriguez1000/Flores_CD`).

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
  - `docs/references/decisions.md` — ACTUALIZADO (D-016 a D-025 registrados)
  - `docs/governance/BRD.md` — ACTUALIZADO (v1.5.0 → v1.6.0 → v1.7.0 → v1.8.0, 14 micro-decisiones acumuladas)
  - `docs/references/decisions.md` — ACTUALIZADO (D-026 a D-030 registrados)
  - `docs/references/handoff.md` — ACTUALIZADO (este archivo)
- Pendiente de commit y merge a rama de iteracion: a cargo del `ai-repository-governor`
