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

---

## [2026-04-28] — Auditoria Devil's Advocate BRD Rondas 1 y 2 (v1.1.0 → v1.3.0)

- **Rama:** `slice/F0-backlog-init`
- **Agente de cierre:** `ai-session-steward`
- **Tareas completadas en sesion:** Auditoria del BRD en 2 rondas iterativas → BRD v1.3.0 Aprobado

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-006** | Despliegue: una unica instancia Streamlit en PC compartida con acceso secuencial de 5 analistas | El cliente no tiene infraestructura de servidores. La concurrencia real es baja (5 analistas, acceso secuencial). Streamlit local es la solucion minima que evita complejidad de redes o autenticacion. | El SAD (T0.10) no debe disenar para concurrencia alta. La arquitectura es single-instance por decision de negocio. |
| **D-007** | Persistencia: SQLite como unico mecanismo de escritura; CSV solo para exportacion de lectura | SQLite resuelve el riesgo de corrupcion de datos que tiene CSV cuando multiples procesos escriben al mismo archivo. CSV queda habilitado unicamente como formato de exportacion (lectura). Actualiza parcialmente D-004. | El Contrato de Datos (T0.12) debe especificar SQLite como backend de escritura. El RF-06 del BRD ya asume este esquema. |
| **D-008** | Control de auto-confirmacion: organizacional, no tecnico. El sistema NO bloquea que un analista confirme su propia prediccion | Implementar un bloqueo tecnico requeriria autenticacion real, lo cual fue descartado (CC-001). El costo de complejidad supera el riesgo de uso indebido dado el contexto de equipo pequeno. El control es via proceso operativo del cliente. | behavior.md (T0.7) no debe incluir escenario de bloqueo tecnico de auto-confirmacion. El flujo de US-03 es permisivo a nivel sistema. |
| **D-009** | Extensibilidad v1.0 restringida a datasets con mismo numero de features (4 numericas), distinto target. Formulario dinamico para N-features requiere CC aprobado | Mantiene el formulario de ingreso simple y testeable en v1.0. Evitar la sobre-ingenieria de un formulario dinamico sin un caso de uso concreto y aprobado. | CA-06 del BRD refleja esta restriccion. El SAD y SpecDD deben documentar este limite explicito de extensibilidad. |
| **D-010** | CA-02 dividida en CA-02a y CA-02b para desbloquear el flujo BDD sin esperar al modelo entrenado | CA-02 original mezclaba la verificacion del mecanismo de advertencia (testeable con mock) con la validacion de inputs especificos post-entrenamiento. Dividirla permite iniciar behavior.md y los tests inmediatamente. | CA-02a habilita T0.7 (behavior.md) sin bloqueo. CA-02b se completa en el anexo de calibracion post Phase Modeling. Los tests de CA-02a usan mock de modelo con prob=[0.45, 0.30, 0.25]. |
| **D-011** | Umbral de baja confianza 0.60: calibrable post-entrenamiento. Criterio objetivo: ≤15% de predicciones del test set de Iris deben activar la advertencia | Un umbral fijo sin criterio de calibracion es un parametro magico. El criterio del 15% hace verificable si el umbral es correcto para la distribucion real de Iris, sin imponer un valor arbitrario permanente. | El valor 0.60 es el punto de partida para Phase Modeling. Si la calibracion arroja que ≤15% del test set activa la advertencia con ese umbral, se mantiene. Si no, se ajusta con CC. |

---

### Lecciones Aprendidas

| # | Leccion | Contexto |
| :--- | :--- | :--- |
| **L-007** | Una segunda ronda de auditoria devil's advocate es necesaria cuando la primera ronda genera cambios estructurales. Los cambios de Ronda 1 pueden introducir nuevas inconsistencias que solo se detectan al releer el BRD completo con ojos frescos. | CA-02 fue "reparada" en Ronda 1 con valores concretos, pero el arreglo creo un nuevo problema: mezclaba un test de mecanismo (mock) con un test de datos reales (post-entrenamiento). Solo Ronda 2 lo detecto. |
| **L-008** | Dividir un criterio de aceptacion en dos (CA-02a / CA-02b) es una tecnica valida para desbloquear el ciclo BDD sin sacrificar la trazabilidad. No hay que esperar a tener todos los insumos para iniciar el behavior.md; se puede avanzar en lo testeable y marcar el resto como pendiente con placeholder explicito. | Sin esta division, T0.7 hubiera quedado bloqueada hasta terminar Phase Modeling. Con la division, CA-02a habilita el 90% del behavior.md inmediatamente. |
| **L-009** | Los parametros calibrables (como thresholds de confianza) deben tener un criterio de aceptacion objetivo desde el BRD, no solo un valor inicial. La pregunta correcta no es "cual es el umbral" sino "como sabemos que el umbral es correcto". | El umbral 0.60 sin criterio de calibracion es un parametro magico. El criterio ≤15% lo convierte en una hipotesis verificable. |

---

## [2026-04-28] — CC-002: Shadow Testing

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.3.0 → v1.4.0)

---

### Control de Cambios

| CC-ID | Cambio | Justificacion | Ficha |
| :--- | :--- | :--- | :--- |
| **CC-002** | Incorporar shadow testing con modelo control y modelo tratamiento | El cliente exige evaluar modelos candidatos en produccion real sin impacto operativo para los analistas. Ejecucion simultanea de ambos modelos; solo el control es visible. | `docs/changes/CC-002.md` |

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-012** | Shadow testing como capacidad de primera clase en v1.0: modelo control (visible) y modelo tratamiento (sombra) con almacenamiento diferenciado | El cliente lo exige para evaluar nuevas versiones sin interrumpir el flujo operativo. La arquitectura de doble modelo desde v1.0 evita una refactorizacion costosa en fases posteriores. | El SAD (T0.10) debe disenar la capa de gestion de modelos con dos artefactos y roles. El SpecDD (T0.11) debe definir el dispatcher de prediccion. El Contrato de Datos (T0.12) debe incluir los 3 nuevos campos del esquema SQLite. behavior.md (T0.7) debe incluir escenarios Gherkin de shadow testing. |
| **D-013** | El modelo tratamiento NO participa en el flujo de estados `pendiente/confirmada/corregida`. Sus registros tienen estado fijo `shadow`. | Mantener el flujo operativo simple para el analista. La comparacion de modelos es una actividad offline del Product Owner, no una tarea del analista. | behavior.md (T0.7) no debe incluir escenarios de revision de predicciones shadow por parte del analista. |
| **D-014** | La promocion del modelo tratamiento a control es manual y requiere CC aprobado. No hay promocion automatica. | Garantiza que toda transicion de modelo en produccion pase por gobernanza explicita. Evita regresiones silenciosas. | El SAD (T0.10) debe documentar el mecanismo de swap de modelos como operacion manual con trazabilidad. |

---

## [2026-04-28] — CC-003: Decision Explicita del Analista 1

- **Rama:** `slice/F0-backlog-init`
- **Agente:** `ai-business-strategist`
- **Documento afectado:** `docs/governance/BRD.md` (v1.4.0 → v1.5.0)

---

### Control de Cambios

| CC-ID | Cambio | Justificacion | Ficha |
| :--- | :--- | :--- | :--- |
| **CC-003** | Agregar decision explicita del Analista 1 (Aceptar/Rechazar) tras cada clasificacion | Sin este mecanismo el escenario de FP de alta confianza quedaba sin cobertura. Ademas provee el ground truth necesario para que el shadow test sea comparable. | `docs/changes/CC-003.md` |

---

### Decisiones

| ID | Decision | Justificacion | Impacto |
| :--- | :--- | :--- | :--- |
| **D-015** | El Analista 1 debe emitir siempre una decision explicita (Aceptar/Rechazar) tras cada clasificacion. Alta confianza + Aceptar = `confirmada_a1` (caso cerrado). Cualquier Rechazo o Baja confianza = `pendiente` (va a revision). | Cierra el gap del Falso Positivo de alta confianza y genera ground truth operativo para el shadow test sin infraestructura adicional. | RF-04 y RF-05 del BRD reflejan la maquina de estados completa. El SpecDD (T0.11) debe definir la interfaz del dispatcher con el parametro de decision del Analista 1. behavior.md (T0.7) debe incluir escenarios Gherkin para los cuatro caminos: alta confianza + acepta, alta confianza + rechaza, baja confianza + acepta, baja confianza + rechaza. |
