# handoff.md — Estado Operativo del Proyecto

> Documento de continuidad. Un agente nuevo debe poder retomar el trabajo leyendo solo este archivo.

- **Fecha de cierre:** 2026-04-28
- **Rama activa:** `slice/F0-backlog-init`
- **Fase:** Phase Discovery
- **Iteracion:** 0.0 — Setup y Gobernanza Inicial
- **Progreso de iteracion:** 58% (sin nuevas tareas completadas — sesion de gobernanza y CC)

---

## Logros de la sesion

| Tarea | Entregable | Estado |
| :--- | :--- | :--- |
| **CC-002 ejecutado** | BRD v1.3.0 → v1.4.0: shadow testing incorporado (RF-08 nuevo, RF-02/RF-05/RF-06 expandidos, KPI-T-05 nuevo, Seccion 11.2) | Completada |
| **CC-003 ejecutado** | BRD v1.4.0 → v1.5.0: mecanismo Aceptar/Rechazar del Analista 1 incorporado (RF-04/RF-05 expandidos, RF-06 ampliado a 20 campos, KPI-T-05 actualizado) | Completada |
| **BRD v1.5.0 aprobado** | `docs/governance/BRD.md` — version vigente | Completada |
| **docs/changes/CC-002.md** | Ficha formal de CC-002 creada | Completada |
| **docs/changes/CC-003.md** | Ficha formal de CC-003 creada | Completada |
| **decisions.md actualizado** | D-012 a D-015 registrados | Completada |

### Detalle de la sesion

**CC-002 — Shadow Testing:**
- El cliente exigio shadow testing. Aprobado y ejecutado.
- RF-08 (nuevo): modelo tratamiento opera en sombra, almacena predicciones con `model_role=tratamiento` e `is_shadow=True`, invisible para los analistas.
- RF-02: nota aclaratoria — prediccion visible = modelo control.
- RF-05: estados `pendiente/confirmada/corregida` solo para modelo control. Tratamiento tiene estado fijo `shadow`.
- RF-06: esquema SQLite de 14 → 17 campos (+`model_id`, +`model_role`, +`is_shadow`).
- KPI-T-05 (nuevo): divergencia de prediccion control vs. tratamiento.
- Seccion 11.2: alcance incluido/excluido del shadow testing.

**CC-003 — Mecanismo Aceptar/Rechazar del Analista 1:**
- Vacio identificado: sin mecanismo de rechazo, los Falsos Positivos de alta confianza no tenian cobertura. Aprobado y ejecutado.
- RF-04 (expandido): tras cada clasificacion, el Analista 1 ve dos botones "Aceptar" y "Rechazar". Si rechaza, selecciona la especie que considera correcta. Nuevo estado `confirmada_a1` para alta confianza aceptada.
- RF-05 (expandido): maquina de estados con 5 transiciones. Vista enriquecida para Analista 2 muestra `especie_analista1`.
- RF-06: esquema SQLite de 17 → 20 campos (+`decision_analista1`, +`especie_analista1`, +`timestamp_decision_analista1`).
- KPI-T-05 (actualizado): ground truth definido por estado — `confirmada_a1` → especie del modelo; `confirmada` → `especie_analista1`; `corregida` → `especie_confirmada`.

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

**Tarea #1 para la proxima sesion:** T0.7 — Redactar `docs/governance/behavior.md`. El agente `ai-business-strategist` debe usar el BRD v1.5.0 como unica fuente de verdad y cubrir los siguientes caminos:
- 4 caminos para el mecanismo Aceptar/Rechazar del Analista 1: alta confianza + acepta, alta confianza + rechaza, baja confianza + acepta, baja confianza + rechaza.
- Escenarios de shadow testing: ejecucion invisible del modelo tratamiento, almacenamiento con `is_shadow=True`, ausencia de exposicion al analista.
- CA-02a y CA-02b del BRD siguen vigentes (CA-02a testeable con mock; CA-02b requiere modelo entrenado, se redacta como placeholder).

---

## Bloqueadores activos

Ninguno. El BRD v1.5.0 esta aprobado y es suficiente para iniciar behavior.md.

---

## Contexto critico para retomar

1. **BRD v1.5.0** (`docs/governance/BRD.md`) es la unica fuente de verdad vigente. Versiones anteriores (v1.3.0, v1.4.0) quedan obsoletas.
2. **Maquina de estados completa (5 transiciones):**
   - Alta confianza + A1 acepta → `confirmada_a1` (caso cerrado, sin Analista 2)
   - Baja confianza + cualquier decision A1 → `pendiente`
   - Alta confianza + A1 rechaza → `pendiente`
   - Analista 2 confirma → `confirmada`
   - Analista 2 corrige → `corregida`
3. **Shadow testing completamente invisible para los analistas.** Ambos modelos ejecutan en paralelo; solo el control es visible. El tratamiento tiene estado fijo `shadow`.
4. **Esquema SQLite: 20 campos.** Los 3 campos de CC-003 (`decision_analista1`, `especie_analista1`, `timestamp_decision_analista1`) son nulos para registros shadow.
5. **KPI-T-05:** Ground truth construido desde el veredicto final del flujo operativo, no desde etiquetas externas. Esto elimina la necesidad de un conjunto de test separado para evaluar los modelos en produccion.
6. **behavior.md debe cubrir 4 caminos para el mecanismo A1** ademas de escenarios de shadow testing. CA-02a y CA-02b del BRD siguen vigentes.
7. **CA-02 dividida en CA-02a y CA-02b.** CA-02a usa mock de modelo con prob=[0.45, 0.30, 0.25] → activa advertencia de baja confianza. CA-02b es el test con input real post-entrenamiento y se completa en anexo de calibracion.
8. **Umbral de baja confianza 0.60:** calibrable post-entrenamiento. Criterio: ≤15% de predicciones del test set de Iris deben activar la advertencia.
9. **Control de auto-confirmacion:** organizacional, no tecnico. El sistema NO bloquea que un analista confirme su propia prediccion.
10. El `config.md` es la fuente de verdad para IDs externos (NotebookLM ID: `7169f5cf-1c59-43ea-aa59-56d5e9f1dff3`, GitHub: `https://github.com/jdrodriguez1000/Flores_CD`).

---

## Estado del repositorio

- Rama activa: `slice/F0-backlog-init`
- Archivos modificados/creados en esta sesion:
  - `docs/governance/BRD.md` — MODIFICADO (v1.3.0 → v1.5.0, 2 CC aplicados)
  - `docs/changes/CC-002.md` — CREADO
  - `docs/changes/CC-003.md` — CREADO
  - `docs/references/decisions.md` — ACTUALIZADO (D-012 a D-015 registrados)
  - `docs/references/handoff.md` — ACTUALIZADO (este archivo)
- Pendiente de commit y merge a rama de iteracion: a cargo del `ai-repository-governor`
