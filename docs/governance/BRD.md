# Business Requirements Document (BRD)
## Proyecto: Flores_CD — Clasificador de Especies Iris

> **Documento:** Business Requirements Document
> **Version:** 1.8.0
> **Estado:** Aprobado
> **Fecha:** 2026-04-28
> **Ultima actualizacion:** 2026-04-29
> **Cambios v1.1.0:** CC-001 (analista_id), correcciones C-2, C-3, I-1, I-2, I-3, I-4 (auditoria devil's advocate)
> **Cambios v1.2.0:** Resolucion vacios C-1 (modelo despliegue multi-usuario) y C-2 (restriccion auto-confirmacion como control organizacional); correcciones I-1 (CA-02), I-2 (umbral 0.60), I-3 (segundo analista incierto), I-4 (alcance extensibilidad), I-5 (timestamp_confirmacion)
> **Cambios v1.3.0:** C-1 CA-02 dividido en CA-02a y CA-02b (desbloqueo secuencia BDD); I-1 CA-06 y RF-07 restringidos a mismo numero de features (Opcion A); I-2 session state RF-01a; I-3 SQLite decidido en RF-06 y RNF-03; M-1 trazabilidad; M-2 firmas; M-3 dataset referencia KPI-T-04; M-4 baseline 100% como proxy declarado
> **Cambios v1.4.0:** CC-002 (shadow testing — modelo control y modelo tratamiento): RF-08 nuevo, aclaracion en RF-02 y RF-05, esquema SQLite expandido a 17 campos en RF-06, KPI-T-05 nuevo, alcance shadow testing en Seccion 11.2
> **Cambios v1.5.0:** CC-003 (decision explicita del Analista 1 — aceptar/rechazar): RF-04 expandido con mecanismo de aceptacion/rechazo y nuevo estado `confirmada_a1`; RF-05 maquina de estados actualizada con vista de Analista 2 enriquecida; RF-06 esquema expandido a 20 campos (+`decision_analista1`, +`especie_analista1`, +`timestamp_decision_analista1`); KPI-T-05 actualizado con veredicto final como ground truth
> **Cambios v1.6.0:** Resolucion de 7 micro-decisiones post-auditoria devil's advocate: G-001 (RF-04 — selector al rechazar excluye especie predicha); G-002 (RF-05 — "Corregir" permite elegir cualquier especie incluyendo la del modelo); G-003 (RF-05 — vista A2 muestra origen del estado pendiente); A-001/I-002 (KPI-T-05 — ground truth simplificado a `especie_confirmada`); A-003/I-001 (RF-06 — dominio completo del campo `estado`); CB-004 (RF-06 — campo `prediction_batch_id` para correlacion control/shadow); A-004 (RF-08 — distincion entre modelo no configurado y fallo en runtime)
> **Cambios v1.7.0:** Resolucion de 5 micro-decisiones post-auditoria (H-002 a H-008): H-002 (RF-05 — origen del pendiente cuando ambos triggers son True: etiqueta combinada); H-004 (RF-05 — label de UI de confirmacion del A2 diferenciado por origen de especie); H-005 (RF-05 — estado de la UI tras decision del A2); H-007 (RF-05 — filtrado tecnico de cola de pendientes por analista_id activo); H-008 (RF-06/RF-08 — prediction_batch_id siempre generado aunque no haya registro shadow)
> **Cambios v1.8.0:** Resolucion de 3 micro-decisiones post-auditoria (A3-H01, A3-H02, A3-H09): A3-H01 (CA-02a — mapping explicito de probabilidades del mock: setosa=0.45, versicolor=0.30, virginica=0.25, especie predicha=setosa); A3-H02 (RF-04 — tabla explicita de valores de especie_analista1 por camino del A1: NULL cuando acepta, especie elegida cuando rechaza); A3-H09 (CA-03 — decision del A1 fijada como "acepta", origen del pendiente = baja_confianza_automatica)
> **Autor:** ai-business-strategist
> **Trazabilidad:** shared_understanding.md (Q1-Q9) → BRD v1.3.0 → behavior.md (pendiente)
> **Aprobado por:** jdrodriguez1000 (Product Owner)

---

## Tabla de Contenidos

1. Resumen Ejecutivo
2. Contexto y Problema
3. Objetivo del Proyecto
4. Encuadre Tecnico del Problema (ML Framing)
5. Requerimientos Funcionales
6. Requerimientos No Funcionales
7. User Stories
8. KPIs y Criterios de Aceptacion
9. Analisis del Costo del Error
10. ROI y Viabilidad
11. Restricciones y Exclusiones
12. Firmas de Aprobacion

---

## 1. Resumen Ejecutivo

**Cliente:** Floristeria CD

**Problema de negocio:** Los analistas de Floristeria CD clasifican manualmente muestras de flores Iris en tres especies. El proceso actual requiere que un primer analista realice la clasificacion, un segundo analista la revise, y cuando ambos no coinciden, se convoca un comite de tres expertos para arbitrar. Este flujo de tres capas genera aproximadamente 200 horas-agente perdidas por semana en revision y arbitraje, impidiendo a cada agente cumplir su meta de 3 analisis por hora.

**Objetivo del proyecto:** Entregar un dashboard interactivo (Streamlit, ejecucion local) que automatice la clasificacion de especies de Iris con alta confianza, reduciendo las activaciones del comite de expertos y liberando capacidad operativa del equipo de analistas.

**ROI esperado:** Una reduccion del 60% en desacuerdos entre analistas liberaria aproximadamente 360 analisis adicionales por semana sin incremento de personal, recuperando capacidad operativa equivalente a ~120 horas-agente semanales.

**Proposito extendido:** El sistema debe servir como plantilla replicable para futuros proyectos de clasificacion botanica del cliente, por lo que su arquitectura debe ser modular y bien documentada.

---

## 2. Contexto y Problema

### 2.1 Situacion Actual

[Fuente: Q1, Q2]

El equipo de Floristeria CD opera con cinco analistas cuya tarea incluye la clasificacion de muestras de flores Iris. El proceso manual actual funciona en tres etapas:

1. **Clasificacion primaria:** Un analista clasifica la muestra y emite su prediccion.
2. **Revision:** Un segundo analista revisa la prediccion. Si ambos coinciden, el caso se cierra (con costo de dos analistas por muestra).
3. **Arbitraje (comite):** Cuando los dos analistas no coinciden, se convoca un comite de tres expertos para reclasificar y tomar la decision final.

Este proceso genera alta friccion operativa: cada desacuerdo consume el tiempo de hasta cinco personas (2 analistas + 3 expertos del comite) y bloquea el avance del pipeline de clasificacion.

### 2.2 Impacto Cuantificado

[Fuente: Q3]

| Metrica operativa | Valor |
| :--- | :--- |
| Analistas activos | 5 |
| Capacidad nominal por analista | 3 analisis/hora + 3 revisiones/hora |
| Horas de trabajo diarias | 8 horas (L-V) |
| Analisis por semana (objetivo) | 600 |
| Revisiones por semana | 600 |
| Desacuerdos que activan comite | ~600 por semana (1 de cada revision) |
| Horas-agente perdidas en arbitraje | ~200 horas/semana |
| Analisis liberables con 60% de reduccion | ~360 analisis adicionales/semana |

El costo real del problema no es la imprecision del analista: es la incapacidad de escalar la capacidad de clasificacion sin contratar personal adicional.

---

## 3. Objetivo del Proyecto

[Fuente: Q1, Q4, Q8]

**Entregable principal:** Dashboard interactivo de clasificacion de flores Iris, accesible localmente desde cualquier navegador web estandar, que permita a cada analista ingresar las medidas de una muestra y obtener la clasificacion del modelo con sus probabilidades y advertencias de confianza.

**Usuarios del sistema:** 5 analistas operativos de Floristeria CD.

**Alcance del dataset:** Iris de Fisher — 150 registros, 4 features numericas (sepal_length, sepal_width, petal_length, petal_width), 3 clases balanceadas (Iris setosa, Iris versicolor, Iris virginica).

**Proposito dual:**
- Resolver el problema de clasificacion de Iris con impacto operativo real.
- Servir como plantilla replicable y modular para futuras clasificaciones botanicas del cliente.

---

## 4. Encuadre Tecnico del Problema (ML Framing)

### 4.1 Taxonomia de la Tarea

[Fuente: Q1, Q4]

| Campo | Valor |
| :--- | :--- |
| **Tipo de tarea** | Clasificacion multiclase supervisada |
| **Numero de clases** | 3 |
| **Clases objetivo** | Iris setosa, Iris versicolor, Iris virginica |
| **Variable objetivo (y)** | Especie de Iris (categorica, 3 valores) |
| **Disponibilidad del label** | Disponible en el dataset de Fisher. No requiere etiquetado adicional. |
| **Features de entrada** | sepal_length, sepal_width, petal_length, petal_width (todas numericas continuas, en centimetros) |

### 4.2 Justificacion del Encuadre

El problema es supervisado porque las etiquetas de especie son conocidas para el dataset de entrenamiento. Es multiclase (no binario) porque hay tres especies mutuamente excluyentes. El dataset de Fisher esta balanceado (50 registros por clase), lo que elimina la necesidad de tecnicas de balanceo y justifica el uso de F1-score macro como metrica primaria en lugar de metricas ponderadas.

### 4.3 Baseline del Proceso Actual

El proceso humano actual no tiene una tasa de acierto cuantificada formalmente. Segun los datos declarados en Q3, la tasa de desacuerdo es aproximadamente del 100% (600 desacuerdos sobre 600 revisiones semanales). Este valor se toma como baseline proxy declarado por el cliente, no como tasa auditada. El modelo debe superar este baseline de manera consistente.

---

## 5. Requerimientos Funcionales

### RF-01: Identificacion del Analista y Formulario de Ingreso de Medidas

[Fuente: Q6 — CC-001]

**RF-01a — Identificacion del Analista:** Al iniciar el dashboard, el sistema debe solicitar al analista un identificador de texto libre (`analista_id`). Este campo es obligatorio para habilitar el formulario de clasificacion. El sistema no valida la unicidad ni la autenticidad del identificador; la responsabilidad de ingresar un ID correcto es organizacional, no tecnica.

**Restriccion de session state:** El campo `analista_id` no debe persistirse en el session state de Streamlit entre recargas de pagina ni entre sesiones de navegador. Cada carga del dashboard debe presentar el campo vacio, forzando al analista a ingresar activamente su identificador. Esto es obligatorio dado el modelo de despliegue en PC compartida (RNF-01) para prevenir que el `analista_id` de un usuario anterior quede activo en la sesion siguiente.

**RF-01b — Formulario de Medidas:** El sistema debe presentar al analista un formulario con cuatro campos de entrada numerica y sus rangos validos:

| Campo | Descripcion | Rango valido |
| :--- | :--- | :--- |
| `sepal_length` | Longitud del sepalo (cm) | 4.3 – 7.9 |
| `sepal_width` | Ancho del sepalo (cm) | 2.0 – 4.4 |
| `petal_length` | Longitud del petalo (cm) | 1.0 – 6.9 |
| `petal_width` | Ancho del petalo (cm) | 0.1 – 2.5 |

El analista debe ingresar los cuatro valores manualmente y presionar el boton "Clasificar" para obtener la prediccion.

**Comportamiento ante valor fuera de rango:** El sistema debe mostrar un mensaje de error descriptivo por campo invalido y bloquear el boton "Clasificar" hasta que todos los valores esten dentro del rango. No se emite ninguna prediccion con inputs invalidos.

### RF-02: Prediccion de Especie con Probabilidades por Clase

[Fuente: Q6]

> **Nota CC-002:** La prediccion mostrada al analista corresponde siempre al **modelo control** (el modelo estable en produccion). Ver RF-08 para el comportamiento del modelo tratamiento.

Al presionar "Clasificar", el sistema debe mostrar:

1. La especie predicha (Iris setosa, Iris versicolor o Iris virginica).
2. Las probabilidades de pertenencia a cada una de las tres clases (ejemplo: "virginica 88%, versicolor 10%, setosa 2%").

### RF-03: Advertencia Visual de Baja Confianza

[Fuente: Q5, Q6]

El sistema debe evaluar el valor maximo de probabilidad entre las tres clases. Si ese valor es inferior a 0.60, debe mostrar una advertencia visual clara al analista indicando que la prediccion tiene baja confianza y requiere revision adicional.

### RF-04: Decision Explicita del Analista 1 y Registro de Estado

[Fuente: Q7 — CC-001, CC-003]

Tras cada clasificacion, el sistema presenta al Analista 1 dos botones de decision:

- **"Aceptar":** El Analista 1 esta de acuerdo con la especie predicha por el modelo control.
- **"Rechazar":** El Analista 1 no esta de acuerdo. Al rechazar, aparece un selector de especie donde el Analista 1 debe elegir la especie que considera correcta antes de enviar el caso a revision. **El selector excluye la especie predicha por el modelo control** — el rechazo implica necesariamente una alternativa distinta. Si el Analista 1 considera que el modelo es correcto pero desea forzar revision, debe usar "Aceptar" (el trigger automatico de baja confianza se encarga de enviar a revision cuando aplica).

**Triggers del estado `pendiente` — dos mecanismos independientes:**

1. **Automatico (baja confianza):** Si `max(prob) < 0.60`, el sistema registra la prediccion como `pendiente` independientemente de la decision del Analista 1. La advertencia visual (RF-03) se muestra de todas formas. Si el Analista 1 acepta un caso de baja confianza, el estado sigue siendo `pendiente` porque el modelo mismo lo ha marcado como incierto.
2. **Manual (rechazo del Analista 1):** Si el Analista 1 rechaza la prediccion (incluso con alta confianza), el sistema registra el estado como `pendiente` y almacena la especie elegida por el Analista 1 en `especie_analista1`.

**Trigger del estado `confirmada_a1`:** Si `max(prob) >= 0.60` Y el Analista 1 acepta la prediccion, el estado se registra como `confirmada_a1` y el caso se cierra sin pasar a revision por Analista 2.

**Tabla de valores de `especie_analista1` por camino del A1 (D-031):**

| Camino | Confianza | Decision A1 | `especie_analista1` | Estado resultante |
| :--- | :--- | :--- | :--- | :--- |
| Alta confianza + acepta | max(prob) >= 0.60 | aceptada | `NULL` | `confirmada_a1` |
| Alta confianza + rechaza | max(prob) >= 0.60 | rechazada | especie elegida por A1 | `pendiente` |
| Baja confianza + acepta | max(prob) < 0.60 | aceptada | `NULL` | `pendiente` |
| Baja confianza + rechaza | max(prob) < 0.60 | rechazada | especie elegida por A1 | `pendiente` |

`especie_analista1` es `NULL` en todos los caminos donde el A1 acepta la prediccion, independientemente del nivel de confianza. En el esquema SQLite la columna admite NULL como valor esperado (no string vacio ni "N/A").

**Control organizacional de auto-confirmacion:** El sistema registra el `analista_id` generador de cada prediccion `pendiente` y lo muestra en la cola de revisiones. El sistema **no bloquea tecnicamente** la confirmacion por el mismo `analista_id` (CC-001, D-008). La responsabilidad de respetar este control es organizacional.

### RF-05: Cola de Revisiones Pendientes y Estados Terminales

[Fuente: Q7, Q9 — CC-001]

> **Nota CC-002:** El flujo de estados aplica **exclusivamente a las predicciones del modelo control**. Los registros del modelo tratamiento tienen estado fijo `shadow` y no participan en la cola de revisiones (ver RF-08).

**Maquina de estados completa del sistema:**

| Trigger | Estado resultante | Requiere Analista 2 |
| :--- | :--- | :--- |
| Alta confianza + Analista 1 acepta | `confirmada_a1` | No — caso cerrado |
| Baja confianza (auto, cualquier decision del Analista 1) | `pendiente` | Si |
| Alta confianza + Analista 1 rechaza | `pendiente` | Si |
| Analista 2 confirma la clasificacion | `confirmada` | — |
| Analista 2 elige especie diferente | `corregida` | — |

El dashboard debe incluir una vista de "cola de revisiones pendientes" con **filtrado tecnico por sesion activa:** la cola muestra unicamente los registros con `estado = pendiente` cuyo `analista_id` sea **distinto** al `analista_id` activo en la sesion actual. El analista no ve sus propios casos pendientes. La consulta SQLite aplica el filtro `WHERE estado = 'pendiente' AND analista_id != [analista_id_sesion]`. (D-026)

**Vista enriquecida para el Analista 2:** La cola de revisiones muestra, por cada prediccion `pendiente`:
- Especie predicha por el modelo control + probabilidades
- **Origen del estado pendiente** — tres valores posibles (D-026):
  - `baja_confianza_automatica`: si `baja_confianza = True` y `decision_analista1 = aceptada`
  - `rechazo_analista1`: si `baja_confianza = False` y `decision_analista1 = rechazada`
  - `baja_confianza_automatica + rechazo_analista1`: si `baja_confianza = True` y `decision_analista1 = rechazada` (ambos triggers activos simultaneamente; el trigger automatico no oculta el rechazo del A1)
- Decision del Analista 1: `aceptada` o `rechazada`
- Especie elegida por el Analista 1 (si rechazo) — campo `especie_analista1`
- `analista_id` del generador

**Acciones del Analista 2:**

| Accion | Estado resultante | Descripcion |
| :--- | :--- | :--- |
| Confirmar | `confirmada` | El Analista 2 acepta la especie a confirmar. `especie_confirmada` = `especie_analista1` si hubo rechazo, o `especie_predicha` si no hubo rechazo. **Label de UI diferenciado (D-027):** si `especie_analista1` tiene valor, el boton muestra "Confirmar especie del Analista 1: [especie_analista1]"; si `especie_analista1` es NULL (A1 acepto), el boton muestra "Confirmar especie del modelo: [especie_predicha]". |
| Corregir | `corregida` | El Analista 2 discrepa del criterio del Analista 1 y selecciona la especie que considera correcta. Puede elegir cualquiera de las tres especies, **incluida la predicha por el modelo control** si considera que el modelo tenia razon. `especie_confirmada` = especie elegida por el Analista 2. |

**Flujo de navegacion tras la decision del A2 (D-028):** Tras registrar su decision (Confirmar o Corregir), el sistema muestra un mensaje de confirmacion ("Caso cerrado correctamente") y el caso desaparece de la cola activa. El A2 permanece en la vista de cola con los casos restantes. El caso cerrado sigue accesible en la vista de historial general (RF-06, lectura).

**No existe transicion a "requiere comite" desde el sistema.** Esta decision es deliberada (D-003).

**Obligacion del Analista 2:** Debe emitir siempre un juicio definitivo. El sistema no provee opcion "tambien incierto". Una vez registrado el estado terminal, el caso se considera cerrado.

### RF-06: Persistencia Local del Historial de Predicciones

[Fuente: Q9 — CC-001]

El sistema debe persistir el historial completo de predicciones en una base de datos SQLite local, accesible en cualquier sesion del dashboard independientemente del analista que lo abra. La exportacion del historial a CSV es una funcionalidad de lectura opcional (no de escritura); el almacenamiento primario es siempre SQLite. El esquema minimo requerido por registro es de 21 campos (ver tabla):

| Campo | Tipo | Descripcion |
| :--- | :--- | :--- |
| `timestamp` | datetime | Momento en que se genero la prediccion |
| `analista_id` | string | Identificador del analista que realizo la prediccion |
| `sepal_length` | float | Input del analista |
| `sepal_width` | float | Input del analista |
| `petal_length` | float | Input del analista |
| `petal_width` | float | Input del analista |
| `especie_predicha` | string | Salida del modelo |
| `prob_setosa` | float | Probabilidad clase Iris setosa |
| `prob_versicolor` | float | Probabilidad clase Iris versicolor |
| `prob_virginica` | float | Probabilidad clase Iris virginica |
| `baja_confianza` | bool | True si max(prob) < 0.60 |
| `estado` | string | `pendiente` / `confirmada_a1` / `confirmada` / `corregida` / `shadow` |
| `analista_confirmador_id` | string | ID del segundo analista. Nulo si estado = `pendiente` o `shadow`. |
| `especie_confirmada` | string | Especie final aceptada. Nulo si estado = `pendiente` o `shadow`. |
| `timestamp_confirmacion` | datetime | Momento en que el segundo analista registro el estado terminal. Nulo si estado = `pendiente` o `shadow`. |
| `model_id` | string | Identificador de version del modelo que genero la prediccion (ej: `control_v1`, `tratamiento_v2`) |
| `model_role` | string | Rol del modelo: `control` o `tratamiento` |
| `is_shadow` | bool | True si la prediccion corresponde al modelo tratamiento (no expuesta al analista) |
| `decision_analista1` | string | Decision del Analista 1: `aceptada` o `rechazada`. Nulo para registros del modelo tratamiento. |
| `especie_analista1` | string | Especie elegida por el Analista 1 al rechazar la prediccion. Nulo si acepto o si es registro shadow. |
| `timestamp_decision_analista1` | datetime | Momento en que el Analista 1 registro su decision. Nulo para registros shadow. |
| `prediction_batch_id` | string (UUID) | Identificador unico del ciclo de prediccion. Generado por el dispatcher y almacenado en el registro del modelo control en **todos** los ciclos, independientemente de si el modelo tratamiento esta configurado. Cuando el tratamiento opera, el mismo UUID se almacena en el registro shadow — permitiendo el join exacto control/shadow para KPI-T-05. Cuando no hay registro shadow (modelo tratamiento no configurado), el UUID queda sin par; esto es esperado y no constituye un error. (D-029) |

### RF-07: Extensibilidad Modular

[Fuente: Q4]

La arquitectura del sistema debe ser modular, de forma que los componentes de ingestion de datos, preprocesamiento, prediccion y visualizacion puedan ser reutilizados o reemplazados para clasificar otras especies botanicas distintas al Iris de Fisher, sin necesidad de reescribir el sistema completo.

**Alcance de la extensibilidad en v1.0:** El sistema soporta la sustitucion del modelo entrenado sobre Iris de Fisher por otro modelo entrenado sobre un dataset con el mismo numero de features numericas (4) y distinto target de clases. La generacion dinamica de formularios para datasets con numero de features diferente queda fuera del alcance de esta version y requerira un Control de Cambios aprobado.

### RF-08: Shadow Testing — Modelo Control y Modelo Tratamiento

[Fuente: CC-002 — requerimiento del cliente 2026-04-28]

El sistema debe soportar la ejecucion simultanea de dos modelos de clasificacion sobre cada input del analista:

**Modelo control:** El modelo estable en produccion. Su prediccion y probabilidades se muestran al analista en el dashboard (RF-02). Toda la logica de advertencia de baja confianza (RF-03), registro de estado (RF-04, RF-05) y persistencia operativa aplica exclusivamente al modelo control.

**Modelo tratamiento:** El modelo candidato bajo evaluacion. Opera en modo sombra: recibe el mismo input que el modelo control de forma simultanea, pero su prediccion NO se expone al analista. Sus resultados se almacenan automaticamente con `model_role = tratamiento` e `is_shadow = True` para analisis de divergencia offline (KPI-T-05). El modelo tratamiento no participa en el flujo de estados `pendiente / confirmada / corregida`.

**Comportamiento requerido:**
1. Al recibir un input del analista, el sistema invoca ambos modelos y genera un `prediction_batch_id` (UUID) compartido para ambos registros.
2. Solo la prediccion del modelo control se muestra en el dashboard.
3. Ambas predicciones se persisten en SQLite con su respectivo `model_id`, `model_role` y `prediction_batch_id`.
4. **Disponibilidad del modelo tratamiento — dos estados distintos:**
   - **No configurado** (artefacto ausente en disco o flag desactivado en configuracion): el sistema opera normalmente con solo el modelo control, sin error ni advertencia al analista. Es un estado esperado de operacion.
   - **Configurado pero falla en runtime** (modelo cargado pero error al inferir): el sistema registra el error en el log interno, no expone ningun mensaje al analista, y continua la operacion con solo el modelo control. El registro del modelo tratamiento no se escribe en SQLite para ese ciclo.

**Transaccion Atomica DB:** La escritura a base de datos debe realizarse mediante un bloque transaccional atómico. Si ocurre un fallo en el Modelo Control (RF-08b), se realiza un ROLLBACK completo, evitando registros shadow huérfanos.

**Restricciones de alcance en v1.0:**
- El sistema NO promueve automaticamente el modelo tratamiento a control. La promocion es una decision manual del Product Owner con CC aprobado.
- El sistema NO implementa division de trafico (A/B testing con split de analistas). Ambos modelos reciben el 100% de los inputs.
- El sistema NO expone los resultados del modelo tratamiento al analista en ninguna vista del dashboard en esta version.

---

## 6. Requerimientos No Funcionales

### RNF-01: Stack Tecnologico

[Fuente: Q8]

El sistema debe construirse exclusivamente con el siguiente stack:

- **Lenguaje:** Python
- **Framework de ML:** scikit-learn
- **Framework de UI:** Streamlit
- **Ejecucion:** Local — una unica instancia Streamlit en una PC compartida a la que los 5 analistas acceden fisicamente de forma secuencial (sin dependencias de infraestructura cloud ni servidores externos)

### RNF-02: Acceso desde Navegador

[Fuente: Q8]

El dashboard debe ser accesible desde cualquier navegador web estandar (Chrome, Firefox, Edge) sin instalacion de software adicional por parte del analista, mas alla del entorno Python local.

### RNF-03: Persistencia Ligera

[Fuente: Q9, D-004]

La capa de persistencia debe implementarse con SQLite local como mecanismo de escritura. No se requieren bases de datos relacionales externas, sistemas de mensajeria ni servicios cloud de almacenamiento.

**Modelo de acceso concurrente:** Dado que el despliegue es una unica instancia en PC compartida (RNF-01), el archivo de persistencia tiene un unico proceso escritor activo en cada momento. No se requiere gestion de concurrencia multi-proceso. **Se decide SQLite como unico mecanismo de escritura** por su integridad transaccional nativa y su comportamiento robusto ante reinicios inesperados. CSV queda reservado exclusivamente como formato de exportacion de lectura (RF-06).

### RNF-04: Reproducibilidad

El modelo entrenado debe ser serializable y reproducible. La version del dataset, del codigo y del modelo deben mantenerse alineadas mediante el linaje definido en CLAUDE.md: VERSION DE DATOS (Gold) → VERSION DE CODIGO (src) → VERSION DE MODELO (models).

### RNF-05: Portabilidad del Repositorio

Todos los enlaces en documentos y referencias en el codigo deben usar rutas relativas respecto a la raiz del proyecto. Se prohiben rutas absolutas para garantizar la movilidad total del repositorio.

---

## 7. User Stories

### US-01: Clasificacion con Alta Confianza

**Como** analista de Floristeria CD,
**quiero** ingresar las 4 medidas de una muestra de Iris en el dashboard y obtener la especie clasificada junto con las probabilidades por clase,
**con** una confianza maxima superior al 60%,
**para** cerrar el caso de clasificacion sin necesidad de revision adicional ni activacion del comite de expertos,
**y ver** el resultado en el dashboard Streamlit de forma inmediata tras presionar "Clasificar".

[Fuente: Q1, Q2, Q6 — Decision D-002]

---

### US-02: Identificacion de Caso de Baja Confianza

**Como** analista de Floristeria CD,
**quiero** recibir una advertencia visual clara cuando la confianza maxima del modelo sea inferior al 60%,
**para** saber que este caso requiere revision por un segundo analista antes de cerrarse,
**y que** la prediccion quede registrada automaticamente como "revision pendiente" sin que yo tenga que activar el comite de expertos.

[Fuente: Q5, Q7 — Decisiones D-001, D-003]

---

### US-03: Revision de Cola de Pendientes

**Como** segundo analista de Floristeria CD,
**quiero** ver en el dashboard la lista de predicciones marcadas como "revision pendiente",
**para** confirmar o corregir cada caso en mi sesion de trabajo,
**y que** el historial quede actualizado con mi confirmacion como registro permanente.

[Fuente: Q7, Q9 — Decisiones D-003, D-004]

---

## 8. KPIs y Criterios de Aceptacion

### 8.1 Metricas Tecnicas del Modelo (DoD Tecnico)

[Fuente: Q5 — Decision D-001]

| ID | Metrica | Threshold | Justificacion |
| :--- | :--- | :--- | :--- |
| **KPI-T-01** | F1-score macro | >= 0.95 | Metrica primaria. Mide el rendimiento equilibrado entre las 3 clases. Dataset balanceado justifica macro sobre weighted. |
| **KPI-T-02** | Accuracy | >= 0.95 | Metrica de comunicacion con stakeholders. Complementa el F1-score. |
| **KPI-T-03** | Recall por clase (Confusion Matrix) | >= 0.90 para cada clase | Detecta confusiones sistematicas, especialmente entre versicolor y virginica. Ningun clase puede ser ignorada por el modelo. |
| **KPI-T-04** | Advertencia de confianza | Activar si max(prob) < 0.60 | Mecanismo operativo para reducir escalaciones al comite. No es metrica del modelo sino del sistema. Umbral acordado con el cliente como valor inicial de operacion; debe calibrarse post-entrenamiento para que no mas del 15% de las predicciones del conjunto de test de Iris de Fisher activen la advertencia. Si la calibracion indica un umbral diferente, se emitira un CC antes del despliegue. |
| **KPI-T-05** | Divergencia y precision relativa control vs. tratamiento | Medicion offline usando el veredicto final como ground truth: (1) `especie_predicha` del control vs. veredicto final; (2) `especie_predicha` del tratamiento vs. veredicto final; (3) % de inputs donde ambos modelos divergen entre si. **Ground truth:** `especie_confirmada` para todos los registros con estado terminal (`confirmada_a1`, `confirmada`, `corregida`). El join entre registro control y registro shadow se realiza via `prediction_batch_id`. No tiene threshold de aceptacion en v1.0; sirve como insumo para la decision de promocion del modelo tratamiento a control. |

**Metricas excluidas:**
- AUC-ROC multiclase: excluida por no aportar valor operativo adicional con dataset balanceado (Decision D-001).

### 8.2 Metricas de Negocio (DoD Operativo)

[Fuente: Q2, Q3]

| ID | Metrica | Objetivo |
| :--- | :--- | :--- |
| **KPI-N-01** | Reduccion de activaciones del comite de expertos | >= 60% respecto al baseline declarado. **Baseline proxy:** 600 activaciones/semana (Q3). **Mecanismo de medicion:** predicciones con estado `pendiente` registradas por semana en el historial local vs. el baseline de 600. Periodo de evaluacion minimo: 4 semanas de uso en produccion. |
| **KPI-N-02** | Capacidad operativa liberada | ~360 analisis adicionales/semana con la misma dotacion de personal |

### 8.3 Criterios de Aceptacion Funcional (UAT)

| ID | Criterio | Condicion de Aprobacion |
| :--- | :--- | :--- |
| **CA-01** | Prediccion con alta confianza | Dado sepal_length=5.1, sepal_width=3.5, petal_length=1.4, petal_width=0.2 → especie predicha: setosa, max(prob) >= 0.60, advertencia NO visible |
| **CA-02a** | Comportamiento del sistema ante baja confianza (testeable sin modelo real) | Dado cualquier input cuyo max(prob) retornado por el modelo (o un mock del modelo) sea < 0.60: advertencia visual VISIBLE y prediccion guardada automaticamente con estado `pendiente`. Este criterio es verificable desde behavior.md con un modelo stub antes del entrenamiento. **Mock de referencia (D-031):** `prob=[setosa=0.45, versicolor=0.30, virginica=0.25]` → especie predicha: `setosa` (max(prob)=0.45 < 0.60). Este es el unico mock canonico para tests de CA-02a; el orden de clases sigue el orden alfabetico de scikit-learn: setosa=0, versicolor=1, virginica=2. |
| **CA-02b** | Input concreto de frontera (fijado post-entrenamiento) | El input especifico del conjunto de test de Iris que produce max(prob) < 0.60 se documenta como anexo de calibracion en behavior.md al momento del primer entrenamiento. Este criterio complementa CA-02a con un caso real del dataset y no bloquea la redaccion de behavior.md. |
| **CA-03** | Cola de revisiones | Dado que el analista `analista_gen` ingresa sepal_length=6.3, sepal_width=2.5, petal_length=4.9, petal_width=1.5 y el mock retorna `prob=[setosa=0.45, versicolor=0.30, virginica=0.25]` (especie predicha: `setosa`, max(prob)=0.45 < 0.60), **y el A1 acepta la prediccion** (`decision_analista1=aceptada`, `especie_analista1=NULL`), entonces el estado es `pendiente` por trigger automatico de baja confianza. Cuando el analista `analista_rev` (diferente a `analista_gen`) abre el dashboard, el registro aparece en la cola con: especie predicha (`setosa`), probabilidades, **origen del pendiente = `baja_confianza_automatica`**, decision del A1 (`aceptada`), `especie_analista1` (NULL), y `analista_id` del generador (`analista_gen`). (D-032) |
| **CA-04** | Persistencia entre sesiones | Dado que existen N registros en SQLite antes de reiniciar el dashboard (N >= 1), cuando el dashboard se reinicia y cualquier analista lo abre, entonces la vista de historial muestra exactamente N registros sin diferencia de contenido. |
| **CA-05** | Thresholds tecnicos | Modelo evaluado en conjunto de test con F1-score macro >= 0.95, Accuracy >= 0.95, Recall por clase >= 0.90 |
| **CA-06** | Extensibilidad modular | El modelo entrenado sobre Iris de Fisher puede reemplazarse por un modelo entrenado sobre un dataset diferente con el mismo numero de features numericas (4) y distinto target de clases, sin modificar el codigo de la capa de UI ni de persistencia. La extension a datasets con diferente numero de features queda fuera del alcance de v1.0 (ver RF-07). |

---

## 9. Analisis del Costo del Error

[Fuente: Q2, Q3 — Protocolo business-to-ml-translator]

### 9.1 Costo del Falso Positivo (FP)

Un Falso Positivo ocurre cuando el modelo predice una especie incorrecta con alta confianza (sin activar la advertencia). En este caso, el error pasaria desapercibido y el caso se cerraria con una clasificacion erronea.

**Impacto:** Clasificacion incorrecta registrada como definitiva. Potencial impacto en decisiones botanicas posteriores del cliente.

**Severidad:** Alta — es el error mas costoso porque no activa el mecanismo de revision.

### 9.2 Costo del Falso Negativo (FN)

Un Falso Negativo en este contexto es cuando el modelo no activa la advertencia de baja confianza en un caso que realmente es ambiguo. Equivalente al FP en consecuencias.

**Impacto:** Caso ambiguo cerrado sin revision adicional.

**Severidad:** Alta.

### 9.3 Costo de la Advertencia Correcta (True Positive de baja confianza)

Cuando el modelo detecta correctamente una prediccion de baja confianza y activa la advertencia, el costo es enviar el caso a revision por un segundo analista. Esto es preferible a la activacion del comite completo (3 expertos), reduciendo el costo operativo de 5 personas (2 analistas + 3 expertos) a 2 personas (analista original + segundo analista revisor).

**Conclusion de penalizacion:** El threshold de F1-score macro >= 0.95 y Recall por clase >= 0.90 son las restricciones criticas. El modelo no puede sacrificar Recall de ninguna clase para ganar Precision. La penalizacion por Falsos Negativos de clase es equivalente a la de Falsos Positivos.

---

## 10. ROI y Viabilidad

[Fuente: Q3, Q4 — Protocolo business-to-ml-translator]

### 10.1 Viabilidad del Proyecto

El dataset Iris de Fisher es un dataset canónico de ML con separabilidad alta entre clases (especialmente Iris setosa). Los thresholds definidos (F1-score >= 0.95) son alcanzables con clasificadores estandar de scikit-learn (Random Forest, SVM, Logistic Regression) sin necesidad de arquitecturas complejas.

**Evaluacion "No Model for Model's Sake":** Una regla IF-ELSE basada en petal_length podria separar Iris setosa del resto con ~97% de precision. Sin embargo, la separacion entre versicolor y virginica requiere un modelo supervisado. La complejidad del modelo esta justificada.

### 10.2 Proyeccion de ROI

| Escenario | Desacuerdos actuales | Reduccion objetivo | Horas-agente recuperadas | Analisis adicionales/semana |
| :--- | :--- | :--- | :--- | :--- |
| **Baseline** | 600/semana | — | — | — |
| **Objetivo (60% reduccion)** | 240/semana | 360 casos | ~120 horas | ~360 analisis |

El ROI no se mide en puntos de accuracy: se mide en capacidad operativa recuperada sin incremento de personal.

---

## 11. Restricciones y Exclusiones

### 11.1 Restricciones Tecnicas

[Fuente: Q8 — Decision D-002]

- El sistema debe ejecutarse localmente. No se contemplan despliegues en cloud, servidores remotos ni contenedores en esta fase.
- El stack esta fijo: Python + scikit-learn + Streamlit. No se pueden agregar frameworks de UI alternativos ni librerias de ML diferentes a scikit-learn sin un Control de Cambios aprobado.

### 11.2 Exclusiones Explicitas del Alcance

[Fuente: Q6 — Decision D-002]

- **Sin ingesta de CSV masivo:** El dashboard no contempla la carga de archivos CSV para clasificacion en lote. La modalidad aprobada es el ingreso manual de medidas individuales.
- **Sin API REST:** No se construira un endpoint REST ni ninguna interfaz programatica de prediccion. El unico punto de entrada al modelo es el formulario del dashboard Streamlit.
- **Sin infraestructura cloud:** No se usaran servicios de AWS, GCP, Azure ni ningun proveedor externo en esta fase.
- **Sin autenticacion de usuarios:** El sistema no implementa gestion de identidad, control de acceso ni validacion de credenciales. El campo `analista_id` (RF-01a, CC-001) es un identificador de texto libre registrado para trazabilidad operativa; el sistema no verifica su autenticidad. Cualquier usuario con acceso local puede ingresar cualquier identificador.
- **Sin reentrenamiento automatico:** El modelo se entrena offline y se serializa. El sistema no implementa pipelines de reentrenamiento continuo en esta fase.
- **Sin AUC-ROC multiclase:** Metrica excluida explicitamente (Decision D-001) por no aportar valor operativo adicional con el dataset balanceado de Iris de Fisher.
- **Shadow testing — alcance incluido y excluido (CC-002):**
  - *Incluido en v1.0:* Ejecucion simultanea de modelo control y modelo tratamiento sobre cada input; almacenamiento diferenciado de predicciones con `model_id`, `model_role` e `is_shadow`; calculo de KPI-T-05 (divergencia offline).
  - *Excluido en v1.0:* Promocion automatica del modelo tratamiento a control; division de trafico entre analistas (A/B testing con split de usuarios); exposicion de predicciones del modelo tratamiento al analista en el dashboard; reentrenamiento automatico del modelo tratamiento.

---

## 12. Firmas de Aprobacion

| Rol | Nombre | Fecha | Estado |
| :--- | :--- | :--- | :--- |
| **Product Owner / Usuario** | jdrodriguez1000 | 2026-04-29 | Aprobado v1.8.0 (historial: v1.1.0, v1.2.0, v1.3.0, v1.4.0, v1.5.0, v1.6.0, v1.7.0) |
| **Agente Responsable (ai-business-strategist)** | claude-sonnet-4-6 | 2026-04-28 | Emitido |

---

> **Trazabilidad documental:**
> - Fuente primaria: `docs/Phase_discovery/shared_understanding.md` (Q1-Q9, firmado 2026-04-28)
> - Decisiones registradas: `docs/references/decisions.md` (D-001 a D-030)
> - Control de cambios: `docs/changes/CC-001.md`, `docs/changes/CC-002.md`
> - Siguiente documento: `docs/governance/behavior.md` (BDD Contract — **T0.7 HABILITADA**; debe incluir escenarios Gherkin de shadow testing basados en RF-08)
> - Metodologia: SpecDD + BDD + TDD segun `CLAUDE.md` y `docs/methodology/process.md`
.7 HABILITADA**; debe incluir escenarios Gherkin de shadow testing basados en RF-08)
> - Metodologia: SpecDD + BDD + TDD segun `CLAUDE.md` y `docs/methodology/process.md`
