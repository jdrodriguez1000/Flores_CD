# decisions.md — Registro Historico de Decisiones y Lecciones Aprendidas

> Registro append-only. Nunca se sobrescribe. Los nuevos registros se agregan al final con separadores claros.
> Prioridad: el "por que" de cada decision es mas valioso que la descripcion tecnica del cambio.

---

## [2026-04-28] — Sesion de Cierre: Phase Discovery / Iteracion 0.0

- **Rama:** `slice/F0-backlog-init`
- **Agente de cierre:** `ai-session-steward`
- **Tareas completadas en sesion:** T0.4

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-001** | Metricas tecnicas aprobadas: F1-score macro >= 0.95, Accuracy >= 0.95, Recall por clase >= 0.90, advertencia de confianza si max(prob) < 0.60 | El usuario aprobo los thresholds recomendados. AUC-ROC multiclase fue descartado porque no aporta valor operativo adicional con dataset balanceado. La advertencia de confianza es el mecanismo clave para reducir escalaciones al comite de expertos. | Define el DoD tecnico del modelo. Todo experimento en Phase Modeling debe medirse contra estos umbrales. |
| **D-002** | Modalidad de interaccion del dashboard: ingreso manual de 4 medidas (no CSV masivo, no API REST) | El caso de uso operativo es clasificacion individual analista por analista. CSV y API fueron descartados explicitamente por el usuario por no corresponder al flujo real de trabajo. | Simplifica la arquitectura del dashboard. No se necesita parser de CSV ni endpoint REST en el alcance actual. |
| **D-003** | Flujo de baja confianza: estado "revision pendiente" con confirmacion por segundo analista (no escalacion directa al comite, no descarte) | Preserva el trabajo realizado, evita convocar al comite de 3 expertos para casos individuales de baja confianza, y reduce el costo operativo cuantificado en ~200 horas-agente/semana. | El dashboard requiere una capa de estado para predicciones ("confirmada" / "pendiente") y una vista de cola de revisiones. Implica persistencia local. |
| **D-004** | Persistencia: CSV o SQLite local (no base de datos remota, no cloud) | Compatible con el stack Streamlit local sin infraestructura adicional. El usuario aprobo explicitamente. Coherente con la restriccion de ejecucion local. | La capa de datos es ligera. No requiere migraciones de esquema complejas para el alcance actual. |
| **D-005** | Doble proposito del proyecto: resolver clasificacion de Iris Y servir como plantilla replicable para otras clasificaciones botanicas | El cliente necesita extender este tipo de trabajos a otras flores. Documentado explicitamente en shared_understanding.md (Q4). | La arquitectura debe ser modular desde el diseno inicial. El SAD (T0.10) y el SpecDD (T0.11) deben reflejar esta restriccion de extensibilidad. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-001** | El ROI del proyecto no es mejora de precision del modelo: es capacidad operativa recuperada. Cuantificar el impacto en horas-agente (no en accuracy) es lo que hace al argumento de negocio convincente. | Surgio en Q3 de la entrevista. La cuantificacion (600 revisiones/semana -> ~200 horas-agente perdidas -> 360 analisis liberables) convirtio un requerimiento vago en un KPI operativo concreto. |
| **L-002** | Antes de definir metricas tecnicas, hay que entender el mecanismo de fallo del proceso humano. El threshold de confianza < 0.60 no es una metrica academica: es el puente entre la salida del modelo y la reduccion del comite de expertos. | El modelo puede tener F1 = 0.98 pero si no tiene mecanismo de advertencia de baja confianza, el flujo operativo de revision no cambia. |
| **L-003** | El shared_understanding.md debe redactarse como fuente de verdad inmutable para el BRD. En T0.6, el agente `ai-business-strategist` no debe reinventar requerimientos: debe traducir directamente las respuestas aprobadas del Historial de Interrogacion a lenguaje de requerimientos formales. | La entrevista ya tiene toda la informacion necesaria. Agregar interpretaciones no validadas en el BRD seria una violacion de Soberania Documental. |

---

## [2026-04-28] — Sesion de Cierre: Phase Discovery / Iteracion 0.0 — T0.5

- **Rama:** `slice/F0-backlog-init`
- **Agente de cierre:** `ai-session-steward`
- **Tareas completadas en sesion:** T0.5

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-006** | Crear `docs/references/config.md` como cedula de identidad oficial del proyecto con todos los IDs externos y metadatos de estado | Un proyecto con multiples agentes necesita una fuente de verdad unica para IDs externos (NotebookLM, GitHub) y estado operativo. Sin este archivo, cada agente debia suponer o reinventar valores como el NOTEBOOK_ID, generando riesgo de inconsistencias. | Todo agente debe leer `config.md` en apertura de sesion. Elimina la necesidad de repetir IDs en el `handoff.md` o en instrucciones ad-hoc. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-004** | Los IDs de herramientas externas (NotebookLM, plataformas cloud) deben centralizarse en un unico archivo de configuracion desde la primera sesion. Pasarlos en el contexto de cada invocacion es fragil y propenso a error humano. | T0.5 fue necesaria precisamente porque el NOTEBOOK_ID se habia estado pasando manualmente en cada invocacion de agente. Centralizar en `config.md` hace el sistema autosuficiente. |

---

## [2026-04-28] — Control de Cambios CC-001 + Auditoria BRD v1.0.0

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.0.0 → v1.1.0)

---

### Control de Cambios

| CC-ID | Cambio | Justificacion | Ficha |
| :--- | :--- | :--- | :--- |
| **CC-001** | Agregar `analista_id` (texto libre) al flujo de prediccion y confirmacion | Contradiccion logica entre RF-04/RF-05 (requerian distinguir analistas) y la exclusion de autenticacion. Solucion minima sin cambio de arquitectura. | `docs/changes/CC-001.md` |

### Correcciones de Auditoria Aplicadas

| ID | Descripcion |
| :--- | :--- |
| **C-2** | CA-02 reemplazado con valores concretos ejecutables (sepal_length=6.3, sepal_width=2.5, petal_length=4.9, petal_width=1.5) |
| **C-3** | RF-05 define tabla de transiciones de estado: `pendiente` → `confirmada` / `corregida`. Cierra el flujo. |
| **I-1** | CA-06 agregado para RF-07: criterio verificable de extensibilidad modular |
| **I-2** | RF-06 incluye esquema minimo de persistencia con 14 campos definidos |
| **I-3** | KPI-N-01 incluye baseline proxy (600/semana) y mecanismo de medicion (4 semanas post-produccion) |
| **I-4** | RF-01b incluye rangos validos por campo y comportamiento bloqueante ante input invalido |

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-005** | Una auditoria devil's advocate del BRD antes de avanzar a T0.7 (BDD) previene que los escenarios Gherkin hereden vacios logicos. El costo de corregir en el BRD es minimo; el costo de corregir en behavior.md + tests es mayor. | La contradiccion CC-001 hubiera generado un RF inimplementable que solo se habria detectado al escribir el escenario Gherkin de US-02/US-03. |
| **L-006** | Los criterios de aceptacion (CA) deben tener valores concretos desde el BRD. Un CA sin inputs numericos especificos no es un test — es una intencion. | CA-02 original decia "valores ambiguos en zona de frontera" sin especificarlos, haciendo el UAT no determinista. |
