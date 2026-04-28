# config.md: Cédula de Identidad del Proyecto

Este documento implementa localmente los protocolos definidos en `CLAUDE.md`. Es la fuente de verdad para todos los metadatos, IDs externos y estado operativo del proyecto. Ningún agente debe inventar o suponer valores aquí registrados.

**Referencia maestra:** [CLAUDE.md](../../CLAUDE.md)

---

## 1. Identidad del Proyecto

| Campo               | Valor                                                      |
| :------------------ | :--------------------------------------------------------- |
| **Nombre completo** | Flores_CD                                                  |
| **Alias corto**     | Flores_CD                                                  |
| **Cliente**         | Floristeria CD                                             |
| **Propietario Git** | jdrodriguez1000                                            |
| **Tipo de problema**| Clasificación multiclase supervisada (3 clases)            |
| **Dataset base**    | Iris de Fisher (150 registros, 4 features numéricas)       |
| **Stack aprobado**  | Python + scikit-learn + Streamlit (ejecución local)        |
| **Fecha de inicio** | 2026-04-28                                                 |

---

## 2. Estado del Proyecto

| Campo                    | Valor                              |
| :----------------------- | :--------------------------------- |
| **Fase actual**          | Phase Discovery                    |
| **Iteración activa**     | Iteración 0.0: Setup y Gobernanza  |
| **Progreso de iteración**| 50%                                |
| **Rama de trabajo**      | `slice/F0-backlog-init`            |
| **Última tarea cerrada** | T0.5 — Configuración de Identidad  |
| **Próxima tarea**        | T0.6 — Redacción del BRD           |

---

## 3. Fuentes de Verdad Externas

### 3.1 Repositorio Git

| Campo              | Valor                                             |
| :----------------- | :------------------------------------------------ |
| **URL GitHub**     | https://github.com/jdrodriguez1000/Flores_CD      |
| **Rama principal** | `main`                                            |
| **Rama de integración** | `dev`                                        |
| **Patrón de ramas de trabajo** | `slice/F[N]-<nombre>`               |

### 3.2 Base de Conocimiento

| Herramienta   | ID / URL                                              | Propósito                                      |
| :------------ | :---------------------------------------------------- | :--------------------------------------------- |
| **NotebookLM**| `7169f5cf-1c59-43ea-aa59-56d5e9f1dff3`               | Cerebro del proyecto: handoff, decisions, fichas de cambio |
| **Wiki/Notion**| No aplica                                            | —                                              |

---

## 4. Referencias de Gobernanza Interna

| Documento       | Ruta                                     | Propósito                                   |
| :-------------- | :--------------------------------------- | :------------------------------------------ |
| **CLAUDE.md**   | `CLAUDE.md`                              | Protocolo maestro de ingeniería             |
| **backlog.md**  | `docs/governance/backlog.md`             | Fuente de verdad de tareas y prioridades    |
| **decisions.md**| `docs/references/decisions.md`           | Log histórico de decisiones                 |
| **handoff.md**  | `docs/references/handoff.md`             | Estado operativo inter-sesión               |
| **directory.md**| `docs/references/directory.md`           | Estructura de carpetas mandatoria           |
| **documents.md**| `docs/references/documents.md`           | Registro de activos de conocimiento         |
| **principles.md**| `docs/references/principles.md`         | Principios de ingeniería del proyecto       |
| **sources.md**  | `docs/references/sources.md`             | URLs de fuentes de verdad tecnológicas      |
