# behavior.md — Contrato de Comportamiento (BDD)

> **Documento:** BDD Contract (Gherkin Scenarios)
> **Version:** 1.6.0
> **Estado:** Certificado (Auditoría Final Devil's Advocate aplicada, CC-004, CC-005, CC-006)
> **Trazabilidad:** BRD v1.8.0 (RF-01 a RF-08, CA-01 a CA-06)
> **Definicion de Terminado (DoD) Global:** El contrato se considera cumplido cuando los tests automatizados (Pytest/Behave) arrojen `GREEN` sobre el 100% de estos escenarios y la auditoría de persistencia verifique la integridad referencial de los registros shadow.

---

## Característica: Clasificador de Especies Iris

**Como** analista de Floristeria CD,
**quiero** utilizar un sistema de clasificación de flores basado en aprendizaje automático,
**para** reducir los desacuerdos operativos y optimizar el proceso de revisión de muestras.

### Reglas de Negocio Base (Contexto)
- **Umbral de confianza:** 0.60.
- **Modelos:** Se ejecutan siempre el Modelo Control (visible) y el Modelo Tratamiento (sombra).
- **Persistencia:** Todos los registros se guardan en SQLite con un `prediction_batch_id` único.
- **Identificación:** El `analista_id` es obligatorio para cada sesión y no persiste entre recargas.
- **Rangos de Input Válidos:**
  - sepal_length: [4.0, 8.0]
  - sepal_width: [2.0, 4.5]
  - petal_length: [1.0, 7.0]
  - petal_width: [0.1, 2.5]

---

### Antecedentes

**Dado** que el sistema tiene cargado el "Modelo Control" y el "Modelo Tratamiento"
**Y** que el analista "analista_gen" ha iniciado sesión en el dashboard
**Y** que la base de datos SQLite está operativa y vacía

---

### 1. Gestión de Entradas y Sesión

#### Escenario: RF-01a — Aislamiento de Session State
**Dado** que el analista "analista_A" ingresó su ID y realizó una clasificación
**Cuando** la página del dashboard se recarga o se abre en una nueva pestaña
**Entonces** el campo `analista_id` debe aparecer vacío
**Y** el formulario de clasificación debe estar bloqueado hasta que se ingrese un nuevo ID.

#### Esquema del Escenario: RF-01b — Bloqueo por Inputs Fuera de Rango (Error de Usuario)
**Cuando** el analista ingresa un <valor> en el campo <caracteristica> (fuera de su rango válido)
**Entonces** el sistema debe mostrar un mensaje de error descriptivo en el campo
**Y** el botón "Clasificar" debe estar deshabilitado para prevenir el envío de datos inválidos.

Ejemplos:
  | caracteristica | valor |
  | sepal_length   | 8.01  |
  | sepal_width    | 1.99  |
  | petal_length   | 7.01  |
  | petal_width    | 0.09  |

#### Escenario: RF-01b — Bloqueo por Inputs Vacíos (Error de Usuario)
**Cuando** el analista deja uno o más campos de medidas vacíos (sin valor numérico)
**Entonces** el sistema debe mostrar un requerimiento de campo obligatorio
**Y** el botón "Clasificar" debe permanecer deshabilitado, evitando el envío de valores nulos o NaN.

---

### 2. Flujo del Analista 1 (Generación)

#### Escenario: US-01 — Clasificación con Alta Confianza (Camino Feliz A1)
**Cuando** el analista ingresa medidas válidas (ej. 5.1, 3.5, 1.4, 0.2)
**Y** presiona el botón "Clasificar"
**Entonces** el botón "Clasificar" debe deshabilitarse transitoriamente mientras se procesa la solicitud
**Y** el sistema debe mostrar la especie predicha "Iris setosa" con prob >= 0.60
**Y** la advertencia de baja confianza NO debe ser visible
**Cuando** el analista presiona el botón "Aceptar"
**Entonces** el botón "Aceptar" debe deshabilitarse transitoriamente para prevenir envíos duplicados
**Y** el sistema debe registrar en SQLite el estado `confirmada_a1` con `especie_analista1 = NULL`
**Y** el sistema debe guardar "Iris setosa" en la columna `especie_confirmada`
**Y** el sistema debe registrar el timestamp exacto de la operación en el campo de fecha correspondiente
**Y** el sistema debe generar un par de registros (control/shadow) con el mismo `prediction_batch_id`.

#### Escenario: US-02/CA-02a — Clasificación con Baja Confianza (Trigger Automático)
**Cuando** se ingresan medidas que retornan prob < 0.60 (Mock D-031: 0.45, 0.30, 0.25)
**Entonces** el botón "Clasificar" debe deshabilitarse transitoriamente mientras se procesa la solicitud
**Y** el sistema debe mostrar la especie predicha "Iris setosa"
**Y** debe mostrar la advertencia visual de baja confianza
**Cuando** el analista presiona el botón "Aceptar"
**Entonces** el botón "Aceptar" debe deshabilitarse transitoriamente para prevenir envíos duplicados
**Y** el sistema debe registrar en SQLite el estado `pendiente` con `especie_analista1 = NULL`
**Y** `baja_confianza` debe ser True
**Y** `decision_analista1` debe ser "aceptada"
**Y** el sistema debe registrar el timestamp exacto de la operación en el campo de fecha correspondiente
**Y** el sistema debe generar un par de registros (control/shadow) con el mismo `prediction_batch_id`.

#### Escenario: US-02 — Rechazo Manual del Analista 1 (Trigger Manual)
**Cuando** la predicción es "Iris virginica" con alta confianza (prob >= 0.60)
**Y** el analista presiona el botón "Rechazar"
**Y** selecciona "Iris versicolor" como especie correcta
**Entonces** el botón "Rechazar" debe deshabilitarse transitoriamente para prevenir envíos duplicados
**Y** el sistema debe registrar en SQLite el estado `pendiente` con `especie_analista1 = Iris versicolor`
**Y** `baja_confianza` debe ser False
**Y** `decision_analista1` debe ser "rechazada"
**Y** el selector de especie al rechazar no debe permitir elegir la especie original "Iris virginica"
**Y** el sistema debe registrar el timestamp exacto de la operación en el campo de fecha correspondiente
**Y** el sistema debe generar un par de registros (control/shadow) con el mismo `prediction_batch_id`.

#### Escenario: US-02 — Rechazo Manual del Analista 1 con Baja Confianza (El Doble Trigger)
**Cuando** se ingresan medidas que retornan prob < 0.60 (Mock D-031: 0.45, 0.30, 0.25)
**Y** el analista presiona el botón "Rechazar"
**Y** selecciona "Iris versicolor" como especie correcta
**Entonces** el botón "Rechazar" debe deshabilitarse transitoriamente para prevenir envíos duplicados
**Y** el sistema debe registrar en SQLite el estado `pendiente`
**Y** `baja_confianza` debe ser True
**Y** `decision_analista1` debe ser "rechazada"
**Y** `especie_analista1` debe ser "Iris versicolor"
**Y** el sistema debe registrar el timestamp exacto de la operación en el campo de fecha correspondiente
**Y** el sistema debe generar un par de registros (control/shadow) con el mismo `prediction_batch_id`.

---

### 3. Flujo del Analista 2 (Revisión)

#### Escenario: RF-05 — Exclusión de Casos Propios en la Cola (Seguridad Operativa)
**Dado** que el analista "analista_gen" generó una predicción `pendiente`
**Cuando** el mismo analista "analista_gen" accede a la cola de revisiones
**Entonces** no debe ver su propio registro en la lista de casos por revisar.

#### Escenario: RF-05 — Visualización de Origen Combinado (Doble Trigger)
**Dado** que existe una predicción con `baja_confianza = True` Y `decision_analista1 = rechazada`
**Y** que el analista "analista_rev" (ID diferente) abre la cola
**Entonces** el origen del pendiente debe mostrarse como "baja_confianza_automatica + rechazo_analista1".

#### Escenario: RF-05 — Renderizado Dinámico de Botones para el Analista 2
**Dado** que el analista "analista_rev" ve la cola de pendientes
**Cuando** el registro proviene de un rechazo del Analista 1 (`especie_analista1` = "Iris versicolor")
**Entonces** el botón debe decir exactamente "Confirmar especie del Analista 1: Iris versicolor"
**Cuando** el registro proviene de una aceptación de baja confianza (`especie_analista1` = NULL, especie_predicha = "Iris setosa")
**Entonces** el botón debe decir exactamente "Confirmar especie del modelo: Iris setosa".

#### Escenario: US-03 — Acción de Confirmar/Corregir/Anular por A2 y Navegación
**Dado** que el analista "analista_rev" selecciona un caso de la cola
**Cuando** presiona "Confirmar"
**Entonces** el botón "Confirmar" debe deshabilitarse transitoriamente para prevenir envíos duplicados
**Y** el sistema debe mostrar "Caso cerrado correctamente"
**Y** el estado en SQLite debe pasar a `confirmada`
**Y** el sistema debe registrar el ID "analista_rev" en la columna `analista_confirmador_id`
**Y** el sistema debe guardar la especie de confirmación en la columna `especie_confirmada`
**Y** el sistema debe registrar el timestamp exacto de la operación en el campo de fecha de resolución
**Y** el analista debe permanecer en la vista de cola para procesar el resto de pendientes, o si no hay más casos, el sistema debe mostrar un mensaje amigable: "No hay tareas pendientes de revisión".
**Cuando** presiona "Corregir"
**Entonces** el sistema debe mostrar un selector con las 3 especies disponibles (sin filtros)
**Y** al elegir una y enviar, el botón debe deshabilitarse transitoriamente para prevenir envíos duplicados
**Y** el estado en SQLite pasa a `corregida`
**Y** el sistema debe registrar el ID "analista_rev" en la columna `analista_confirmador_id`
**Y** el sistema debe guardar la especie elegida en la columna `especie_confirmada`
**Y** el sistema debe registrar el timestamp exacto de la operación en el campo de fecha de resolución
**Y** el analista debe permanecer en la vista de cola, o ver el mensaje de cola vacía si correspondiera.
**Cuando** presiona "Anular"
**Entonces** el botón "Anular" debe deshabilitarse transitoriamente para prevenir envíos duplicados
**Y** el sistema debe mostrar "Registro anulado y excluido de métricas"
**Y** el estado en SQLite debe pasar a `anulada`
**Y** el sistema debe registrar el ID "analista_rev" en la columna `analista_confirmador_id`
**Y** `especie_confirmada` debe permanecer NULL
**Y** el sistema debe registrar el timestamp exacto de la operación en el campo de fecha de resolución
**Y** el analista debe permanecer en la vista de cola, o ver el mensaje de cola vacía si correspondiera.

---

### 4. Shadow Testing e Integridad

#### Escenario: RF-08 — Operación con Modelo Tratamiento "No Configurado"
**Dado** que el Modelo Tratamiento no está configurado en el sistema
**Cuando** el analista realiza una clasificación
**Entonces** el flujo de la interfaz funciona normalmente usando el Modelo Control
**Y** el sistema genera un UUID (`prediction_batch_id`) para el registro del Modelo Control
**Y** NO se genera ningún registro shadow
**Y** el UUID del Modelo Control queda sin par en la base de datos (comportamiento esperado).

#### Escenario: RF-08 — Shadow Testing (Ejecución Invisible y Mutabilidad Cero)
**Cuando** ambos modelos están configurados y el analista realiza cualquier clasificación
**Entonces** el sistema debe persistir un registro con `estado = 'shadow'`
**Y** los campos `analista_id`, `decision_analista1`, `especie_analista1`, `analista_confirmador_id` y `especie_confirmada` del registro shadow deben ser estrictamente `NULL` permanentemente tras cualquier acción del Analista
**Y** la interfaz de usuario no debe contener ninguna referencia visual al Modelo Tratamiento ni a sus probabilidades.

#### Escenario: RF-08 — Resiliencia ante Fallo del Modelo Tratamiento
**Dado** que el Modelo Tratamiento genera un error de runtime inesperado
**Cuando** el analista presiona "Clasificar"
**Entonces** el sistema debe mostrar la predicción del Modelo Control normalmente
**Y** el analista no debe recibir ninguna notificación del error (silencioso para el usuario)
**Y** en SQLite solo debe existir el registro del Modelo Control para ese batch.

#### Escenario: RF-08b — Resiliencia ante Fallo del Modelo Control
**Dado** que el Modelo Control genera un error de runtime inesperado
**Cuando** el analista presiona "Clasificar"
**Entonces** el sistema debe interceptar el error y mostrar un mensaje controlado en la UI (ej. "Error interno del motor de IA. Contacte a soporte.")
**Y** el botón "Aceptar/Rechazar" debe ocultarse
**Y** el sistema debe aplicar un ROLLBACK transaccional para garantizar que ni el control ni el shadow generen registros huérfanos
**Y** no se debe escribir ningún registro corrupto en SQLite.

---

## Matriz de Definición de Terminado (DoD) Integral

| Funcionalidad | Criterio de Aceptación (DoD) | Verificación Técnica (DoD) |
| :--- | :--- | :--- |
| **Ingreso (RF-01)** | Botón bloqueado ante valores fuera de rango o vacíos. | Test unitario: `validate_form(inputs) == False`. |
| **Sesión (RF-01a)** | ID vacío tras F5 o cambio de pestaña. | Test E2E: Recarga de página limpia el input. |
| **A1 (RF-04)** | 4 caminos de generación de estado cubiertos y persistidos. | Auditoría DB: Verificación de los 4 estados resultantes y timestamps de creación. |
| **Baja Confianza** | Advertencia visible si max(prob) < 0.60. | Mock: Inyección de `prob=0.45` activa CSS alert. |
| **Doble Trigger** | Registro correcto de `baja_confianza=True` y `rechazada`. | Test DB: Valores correctos en tabla. |
| **Revisión (RF-05)** | Ocultamiento de registros del mismo `analista_id`. | Test de integración: Query con `WHERE user != current`. |
| **UI Dinámica (RF-05)**| Labels del botón confirmar reflejan el origen de la especie. | Test E2E/UI: Aserción exacta de texto en el botón. |
| **Corregir (RF-05)** | Selector A2 muestra todas las especies. | Verificación UI: Lista completa de 3 especies. |
| **A2 (RF-05)** | Transición a estados terminales, guardado especie final y analista. | Test DB: `estado IN ('confirmada', 'corregida')`, `especie_confirmada` IS NOT NULL, `analista_confirmador_id` IS NOT NULL, y timestamp de resolución registrado. |
| **Anulación A2** | Transición a estado anulada, exclusión de KPI. | Test DB: `estado = 'anulada'`, `especie_confirmada` IS NULL, exclusión de cálculo de KPI. |
| **Doble Submit** | Bloqueo transitorio de botones en envíos. | Test UI/E2E: Intentos múltiples de click retornan validación de UI bloqueada o estado transaccional único. |
| **Navegación** | A2 se mantiene en la cola o ve "Zero State" si está vacía. | Test E2E: No hay redirección, UI maneja estado vacío correctamente. |
| **Shadow Inactivo** | Operación normal y UUID sin par si el Tratamiento no está. | Auditoría DB: 1 registro por batch, sin errores. |
| **Shadow Activo** | Join exacto, invisibilidad en UI y Mutabilidad cero. | Auditoría DB: `estado='shadow'`, `control.UUID == shadow.UUID`, campos de interacción humana estrictamente `NULL`. UI: Ausencia de ID. |
| **Resiliencia Shadow**| Fallo del shadow no detiene el control. | Inyección de `Exception` en shadow; control arroja `GREEN`. |
| **Resiliencia Control**| Fallo del control detiene flujo y muestra error amigable. | Inyección de `Exception` en control; UI muestra error de soporte, DB limpia. |