# Business Requirements Document (BRD)
## Proyecto: Flores_CD — Clasificador de Especies Iris

> **Documento:** Business Requirements Document
> **Version:** 1.3.0
> **Estado:** Aprobado
> **Fecha:** 2026-04-28
> **Ultima actualizacion:** 2026-04-28
> **Cambios v1.1.0:** CC-001 (analista_id), correcciones C-2, C-3, I-1, I-2, I-3, I-4 (auditoria devil's advocate)
> **Cambios v1.2.0:** Resolucion vacios C-1 (modelo despliegue multi-usuario) y C-2 (restriccion auto-confirmacion como control organizacional); correcciones I-1 (CA-02), I-2 (umbral 0.60), I-3 (segundo analista incierto), I-4 (alcance extensibilidad), I-5 (timestamp_confirmacion)
> **Cambios v1.3.0:** C-1 CA-02 dividido en CA-02a y CA-02b (desbloqueo secuencia BDD); I-1 CA-06 y RF-07 restringidos a mismo numero de features (Opcion A); I-2 session state RF-01a; I-3 SQLite decidido en RF-06 y RNF-03; M-1 trazabilidad; M-2 firmas; M-3 dataset referencia KPI-T-04; M-4 baseline 100% como proxy declarado
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

Al presionar "Clasificar", el sistema debe mostrar:

1. La especie predicha (Iris setosa, Iris versicolor o Iris virginica).
2. Las probabilidades de pertenencia a cada una de las tres clases (ejemplo: "virginica 88%, versicolor 10%, setosa 2%").

### RF-03: Advertencia Visual de Baja Confianza

[Fuente: Q5, Q6]

El sistema debe evaluar el valor maximo de probabilidad entre las tres clases. Si ese valor es inferior a 0.60, debe mostrar una advertencia visual clara al analista indicando que la prediccion tiene baja confianza y requiere revision adicional.

### RF-04: Registro de Prediccion como "Revision Pendiente"

[Fuente: Q7 — CC-001]

Cuando el modelo emite una advertencia de baja confianza (max(prob) < 0.60), el sistema debe registrar automaticamente la prediccion con estado `pendiente` en el almacenamiento local, incluyendo el `analista_id` del analista que genero la prediccion. El analista no puede activar el comite directamente desde el sistema; el flujo correcto es que un segundo analista (con `analista_id` diferente) confirme la prediccion en una sesion posterior.

**Control organizacional de auto-confirmacion:** El sistema registra el `analista_id` generador de cada prediccion `pendiente` y lo muestra en la cola de revisiones para que el segundo analista pueda identificar el origen. Sin embargo, el sistema **no bloquea tecnicamente** la confirmacion por parte del mismo `analista_id`, dado que no valida la autenticidad del identificador (RF-01a). La responsabilidad de respetar este control es organizacional: el proceso interno de Floristeria CD exige que un analista diferente al generador sea quien confirme o corrija cada prediccion pendiente.

### RF-05: Cola de Revisiones Pendientes y Estados Terminales

[Fuente: Q7, Q9 — CC-001]

El dashboard debe incluir una vista de "cola de revisiones pendientes" donde cualquier analista (con `analista_id` diferente al generador) pueda ver las predicciones con estado `pendiente` y resolverlas. Las transiciones de estado permitidas son:

| Accion del segundo analista | Estado resultante | Descripcion |
| :--- | :--- | :--- |
| Confirma la especie predicha | `confirmada` | El segundo analista acepta la clasificacion del modelo. `especie_confirmada` = `especie_predicha`. |
| Corrige la especie predicha | `corregida` | El segundo analista selecciona una especie diferente. Se registran ambas: `especie_predicha` (original del modelo) y `especie_confirmada` (correccion del analista). |

**No existe transicion a "requiere comite" desde el sistema.** Esta decision es deliberada (D-003): el flujo de baja confianza termina siempre con la confirmacion o correccion por un segundo analista, sin activar el comite de tres expertos.

**Obligacion del segundo analista:** El segundo analista debe emitir siempre un juicio definitivo (`confirmada` o `corregida`). El sistema no provee una opcion "tambien incierto". Si el segundo analista tiene dudas, debe consultar fisicamente con un experto antes de registrar su decision en el sistema. Una vez registrado el estado terminal, el caso se considera cerrado.

### RF-06: Persistencia Local del Historial de Predicciones

[Fuente: Q9 — CC-001]

El sistema debe persistir el historial completo de predicciones en una base de datos SQLite local, accesible en cualquier sesion del dashboard independientemente del analista que lo abra. La exportacion del historial a CSV es una funcionalidad de lectura opcional (no de escritura); el almacenamiento primario es siempre SQLite. El esquema minimo requerido por registro es:

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
| `estado` | string | `pendiente` / `confirmada` / `corregida` |
| `analista_confirmador_id` | string | ID del segundo analista. Nulo si estado = `pendiente`. |
| `especie_confirmada` | string | Especie final aceptada. Nulo si estado = `pendiente`. |
| `timestamp_confirmacion` | datetime | Momento en que el segundo analista registro el estado terminal. Nulo si estado = `pendiente`. |

### RF-07: Extensibilidad Modular

[Fuente: Q4]

La arquitectura del sistema debe ser modular, de forma que los componentes de ingestion de datos, preprocesamiento, prediccion y visualizacion puedan ser reutilizados o reemplazados para clasificar otras especies botanicas distintas al Iris de Fisher, sin necesidad de reescribir el sistema completo.

**Alcance de la extensibilidad en v1.0:** El sistema soporta la sustitucion del modelo entrenado sobre Iris de Fisher por otro modelo entrenado sobre un dataset con el mismo numero de features numericas (4) y distinto target de clases. La generacion dinamica de formularios para datasets con numero de features diferente queda fuera del alcance de esta version y requerira un Control de Cambios aprobado.

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
| **CA-02a** | Comportamiento del sistema ante baja confianza (testeable sin modelo real) | Dado cualquier input cuyo max(prob) retornado por el modelo (o un mock del modelo) sea < 0.60: advertencia visual VISIBLE y prediccion guardada automaticamente con estado `pendiente`. Este criterio es verificable desde behavior.md con un modelo stub antes del entrenamiento. |
| **CA-02b** | Input concreto de frontera (fijado post-entrenamiento) | El input especifico del conjunto de test de Iris que produce max(prob) < 0.60 se documenta como anexo de calibracion en behavior.md al momento del primer entrenamiento. Este criterio complementa CA-02a con un caso real del dataset y no bloquea la redaccion de behavior.md. |
| **CA-03** | Cola de revisiones | Prediccion con estado "pendiente" visible en la vista de cola desde cualquier sesion posterior |
| **CA-04** | Persistencia entre sesiones | Historial de predicciones disponible al reiniciar el dashboard sin perdida de datos |
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

---

## 12. Firmas de Aprobacion

| Rol | Nombre | Fecha | Estado |
| :--- | :--- | :--- | :--- |
| **Product Owner / Usuario** | jdrodriguez1000 | 2026-04-28 | Aprobado v1.3.0 (historial: v1.1.0, v1.2.0) |
| **Agente Responsable (ai-business-strategist)** | claude-sonnet-4-6 | 2026-04-28 | Emitido |

---

> **Trazabilidad documental:**
> - Fuente primaria: `docs/Phase_discovery/shared_understanding.md` (Q1-Q9, firmado 2026-04-28)
> - Decisiones registradas: `docs/references/decisions.md` (D-001 a D-005)
> - Siguiente documento: `docs/governance/behavior.md` (BDD Contract — pendiente T0.7)
> - Metodologia: SpecDD + BDD + TDD segun `CLAUDE.md` y `docs/methodology/process.md`
