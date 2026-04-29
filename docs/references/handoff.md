# handoff.md — Estado Operativo del Proyecto

> Documento de continuidad. Un agente nuevo debe poder retomar el trabajo leyendo solo este archivo.

- **Fecha de cierre:** 2026-04-28
- **Rama activa:** `slice/F0-backlog-init`
- **Fase:** Phase Discovery
- **Iteracion:** 0.0 — Setup y Gobernanza Inicial
- **Progreso de iteracion:** 65% (sesion de gobernanza de agentes: Torneo de Algoritmos + Shadow Testing)

---

## Logros de la sesion

| Tarea | Entregable | Estado |
| :--- | :--- | :--- |
| **Agentes actualizados (x6)** | `.claude/agents/ai-data-scientist.md` — Nuevos triggers, mision operativa reescrita, 3 nuevas Reglas de Oro | Completada |
| **Skill actualizado** | `.claude/skills/algorithm-architecture-evaluator/SKILL.md` — Filtro de Eficiencia, Round Robin con CV std, Seleccion Dual con artefacto YAML | Completada |
| **Skill actualizado** | `.claude/skills/baseline-model-developer/SKILL.md` — Baseline como Modelo Control, Challenger como Tratamiento, preparacion del Shadow Test | Completada |
| **Skill actualizado** | `.claude/skills/hyperparameter-optimization-expert/SKILL.md` — Optimizacion diferenciada por rol (Control vs. Tratamiento), estudios Optuna separados | Completada |
| **Skill actualizado** | `.claude/skills/feature-importance-analyzer/SKILL.md` — SHAP y Permutation Importance aplicados a ambos modelos, tabla comparativa, alerta de divergencia SHAP | Completada |
| **Agente actualizado** | `.claude/agents/ai-business-strategist.md` — Hard Rule #6 Shadow Test Intent incorporada | Completada |

### Detalle de la sesion

**Paradigma de Torneo de Algoritmos:**
- El escuadrón de agentes ahora soporta el flujo completo: Torneo → Filtro de Eficiencia → Seleccion Dual (Control/Tratamiento) → Shadow Test.
- El Modelo Control se selecciona por estabilidad (minimo CV std). El Modelo Tratamiento se selecciona por maxima precision, con el Efficiency Gate como filtro previo no negociable.
- El artefacto de salida del torneo es un YAML con `control_model` y `treatment_model` como campos diferenciados.

**Efficiency Gate:**
- Candidatos al torneo son eliminados ANTES de evaluar precision si superan los limites de train time, latencia de inferencia o uso de memoria.
- Este filtro hace al sistema compatible con el hardware del cliente (Streamlit local, PC compartida).

**Rol del ai-business-strategist en Shadow Testing:**
- La Hard Rule #6 obliga al estratega a preguntar explicitamente sobre Shadow Testing durante el ritual ask-me de la Fase 0.
- Las preguntas de seguimiento cubren: quien aprueba el paso a produccion y cuales son los criterios de aceptacion del tratamiento.
- Esto centraliza la captura de la intencion de Shadow Testing en la Fase de Descubrimiento, evitando que se descubra tardıamente como un requisito no documentado.

---

## Pendientes (proxima sesion)

| ID | Tarea | Agente Responsable | Dependencia |
| :--- | :--- | :--- | :--- |
| **T0.7** | Redactar `docs/governance/behavior.md` (BDD Contract / escenarios Gherkin) | `ai-business-strategist` (skill: `gherkin-scenario-author`) | BRD v1.5.0 aprobado |
| **T0.8** | Reporte de Factibilidad de Datos | `ai-data-auditor` | T0.4 resuelta |
| **T0.9** | Construccion del Mockup Visual | `ai-ux-designer` | BRD v1.5.0 |
| **T0.10** | Diseno de Arquitectura de Software (SAD) | `ai-solutions-architect` | T0.6 resuelta, T0.7 |
| **T0.11** | Especificacion de Interfaces (SpecDD) | `ai-solutions-architect` | T0.10 |
| **T0.12** | Creacion del Contrato de Datos | `ai-solutions-architect` | T0.10 |
| **Pendiente opcional** | Ajustar `docs/Phase_discovery/BRD.md` para incorporar seccion de Estrategia de Despliegue con Shadow Testing si aplica al proyecto Flores_CD | `ai-business-strategist` | BRD v1.5.0, decision del cliente |

**Tarea #1 para la proxima sesion:** T0.7 — Redactar `docs/governance/behavior.md`. El agente `ai-business-strategist` debe usar el BRD v1.5.0 como unica fuente de verdad y cubrir los siguientes caminos:
- 4 caminos para el mecanismo Aceptar/Rechazar del Analista 1: alta confianza + acepta, alta confianza + rechaza, baja confianza + acepta, baja confianza + rechaza.
- Escenarios de shadow testing: ejecucion invisible del modelo tratamiento, almacenamiento con `is_shadow=True`, ausencia de exposicion al analista.
- CA-02a y CA-02b del BRD siguen vigentes (CA-02a testeable con mock; CA-02b requiere modelo entrenado, se redacta como placeholder).

---

## Bloqueadores activos

Ninguno. El BRD v1.5.0 esta aprobado y el escuadron de agentes esta alineado con el paradigma de Torneo + Shadow Testing.

---

## Contexto critico para retomar

1. **BRD v1.5.0** (`docs/governance/BRD.md`) es la unica fuente de verdad vigente. Versiones anteriores quedan obsoletas.
2. **Maquina de estados completa (5 transiciones):**
   - Alta confianza + A1 acepta → `confirmada_a1` (caso cerrado, sin Analista 2)
   - Baja confianza + cualquier decision A1 → `pendiente`
   - Alta confianza + A1 rechaza → `pendiente`
   - Analista 2 confirma → `confirmada`
   - Analista 2 corrige → `corregida`
3. **Shadow testing completamente invisible para los analistas.** Ambos modelos ejecutan en paralelo; solo el control es visible. El tratamiento tiene estado fijo `shadow`.
4. **Esquema SQLite: 20 campos.** Los 3 campos de CC-003 (`decision_analista1`, `especie_analista1`, `timestamp_decision_analista1`) son nulos para registros shadow.
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
  - `docs/references/decisions.md` — ACTUALIZADO (D-016 a D-018 registrados)
  - `docs/references/handoff.md` — ACTUALIZADO (este archivo)
- Pendiente de commit y merge a rama de iteracion: a cargo del `ai-repository-governor`
