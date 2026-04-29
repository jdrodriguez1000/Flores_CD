# behavior.md — Contrato de Comportamiento (BDD)

> **Documento:** BDD Contract (Gherkin Scenarios)
> **Version:** 1.0.0
> **Estado:** Borrador (T0.7)
> **Trazabilidad:** BRD v1.8.0 (RF-01 a RF-08, CA-01 a CA-06)
> **Definicion de Terminado (DoD):** Este documento se considera finalizado cuando todos los escenarios Gherkin pasen a estado `GREEN` mediante tests automatizados en la Phase Engineering.

---

## Característica: Clasificador de Especies Iris

**Como** analista de Floristeria CD,
**quiero** utilizar un sistema de clasificación de flores basado en aprendizaje automático,
**para** reducir los desacuerdos operativos y optimizar el proceso de revisión de muestras.

### Reglas de Negocio Base (Contexto)
- **Umbral de confianza:** 0.60.
- **Modelos:** Se ejecutan siempre el Modelo Control (visible) y el Modelo Tratamiento (sombra).
- **Persistencia:** Todos los registros se guardan en SQLite con un `prediction_batch_id` único.
- **Identificación:** El `analista_id` es obligatorio para cada sesión.

---

### Antecedentes

**Dado** que el sistema tiene cargado el "Modelo Control" y el "Modelo Tratamiento"
**Y** que el analista "analista_gen" ha iniciado sesión en el dashboard
**Y** que la base de datos SQLite está operativa y vacía

---

### Escenario: US-01 — Clasificación con Alta Confianza (Camino Feliz A1)

**Cuando** el analista ingresa las siguientes medidas:
  | sepal_length | sepal_width | petal_length | petal_width |
  | :----------- | :---------- | :----------- | :---------- |
  | 5.1          | 3.5         | 1.4          | 0.2         |
**Y** presiona el botón "Clasificar"
**Entonces** el sistema debe mostrar la especie predicha "Iris setosa"
**Y** debe mostrar una probabilidad para "Iris setosa" mayor o igual a 0.60
**Y** la advertencia de baja confianza NO debe ser visible
**Cuando** el analista presiona el botón "Aceptar"
**Entonces** el sistema debe registrar en SQLite un nuevo registro con:
  | campo               | valor            |
  | :------------------ | :--------------- |
  | especie_predicha    | Iris setosa      |
  | baja_confianza      | False            |
  | decision_analista1  | aceptada         |
  | especie_analista1   | NULL             |
  | estado              | confirmada_a1    |
  | model_role          | control          |
  | is_shadow           | False            |
**Y** el sistema debe registrar simultáneamente un registro shadow con `model_role = tratamiento` e `is_shadow = True` compartiendo el mismo `prediction_batch_id`.

---

### Escenario: US-02/CA-02a — Clasificación con Baja Confianza (Trigger Automático)

**Cuando** el analista ingresa las siguientes medidas (CA-02a):
  | sepal_length | sepal_width | petal_length | petal_width |
  | :----------- | :---------- | :----------- | :---------- |
  | 6.3          | 2.5         | 4.9          | 1.5         |
**Y** el modelo retorna las siguientes probabilidades (Mock D-031):
  | Iris setosa | Iris versicolor | Iris virginica |
  | :---------- | :-------------- | :------------- |
  | 0.45        | 0.30            | 0.25           |
**Entonces** el sistema debe mostrar la especie predicha "Iris setosa"
**Y** debe mostrar la advertencia visual de baja confianza
**Cuando** el analista presiona el botón "Aceptar"
**Entonces** el sistema debe registrar en SQLite un registro con:
  | campo               | valor            |
  | :------------------ | :--------------- |
  | especie_predicha    | Iris setosa      |
  | baja_confianza      | True             |
  | decision_analista1  | aceptada         |
  | especie_analista1   | NULL             |
  | estado              | pendiente        |
**Y** el caso debe aparecer en la cola de revisiones para otros analistas.

---

### Escenario: US-02 — Rechazo Manual del Analista 1 (Trigger Manual)

**Cuando** el analista ingresa medidas que resultan en alta confianza para "Iris virginica"
**Y** el analista presiona el botón "Rechazar"
**Y** selecciona "Iris versicolor" como especie correcta
**Entonces** el sistema debe registrar en SQLite un registro con:
  | campo               | valor            |
  | :------------------ | :--------------- |
  | especie_predicha    | Iris virginica   |
  | baja_confianza      | False            |
  | decision_analista1  | rechazada        |
  | especie_analista1   | Iris versicolor  |
  | estado              | pendiente        |
**Y** el selector de especie al rechazar no debe permitir elegir "Iris virginica".

---

### Escenario: US-03 — Revisión por Analista 2 (Confirmar Modelo)

**Dado** que existe una predicción pendiente generada por "analista_gen" con:
  | especie_predicha | baja_confianza | decision_analista1 | especie_analista1 |
  | :--------------- | :------------- | :----------------- | :---------------- |
  | Iris setosa      | True           | aceptada           | NULL              |
**Y** que el analista "analista_rev" inicia sesión en el dashboard
**Cuando** accede a la "Cola de Revisiones Pendientes"
**Entonces** debe ver el caso de "analista_gen" con el origen "baja_confianza_automatica"
**Y** el botón de acción debe decir "Confirmar especie del modelo: Iris setosa"
**Cuando** presiona dicho botón de confirmar
**Entonces** el sistema debe mostrar el mensaje "Caso cerrado correctamente"
**Y** el registro en SQLite debe actualizarse con:
  | campo                   | valor           |
  | :---------------------- | :-------------- |
  | estado                  | confirmada      |
  | analista_confirmador_id | analista_rev    |
  | especie_confirmada      | Iris setosa     |

---

### Escenario: US-03 — Revisión por Analista 2 (Confirmar Analista 1)

**Dado** que existe una predicción pendiente generada por "analista_gen" con:
  | especie_predicha | baja_confianza | decision_analista1 | especie_analista1 |
  | :--------------- | :------------- | :----------------- | :---------------- |
  | Iris virginica   | False          | rechazada          | Iris versicolor   |
**Y** que el analista "analista_rev" inicia sesión
**Cuando** accede a la "Cola de Revisiones Pendientes"
**Entonces** debe ver el caso con el origen "rechazo_analista1"
**Y** el botón de acción debe decir "Confirmar especie del Analista 1: Iris versicolor"
**Cuando** presiona dicho botón de confirmar
**Entonces** el registro en SQLite debe actualizarse con:
  | campo                   | valor            |
  | :---------------------- | :--------------- |
  | estado                  | confirmada       |
  | analista_confirmador_id | analista_rev     |
  | especie_confirmada      | Iris versicolor  |

---

### Escenario: US-03 — Revisión por Analista 2 (Corregir)

**Dado** que existe una predicción pendiente en la cola
**Cuando** el analista "analista_rev" presiona el botón "Corregir"
**Y** selecciona una especie diferente a la propuesta en la cola
**Entonces** el registro en SQLite debe actualizarse con:
  | campo                   | valor            |
  | :---------------------- | :--------------- |
  | estado                  | corregida        |
  | analista_confirmador_id | analista_rev     |
  | especie_confirmada      | especie elegida  |

---

### Escenario: RF-08 — Shadow Testing (Ejecución Invisible)

**Cuando** el analista realiza cualquier clasificación exitosa
**Entonces** el sistema debe generar internamente dos registros con el mismo `prediction_batch_id`
**Y** el registro del Modelo Control debe tener `is_shadow = False`
**Y** el registro del Modelo Tratamiento debe tener `is_shadow = True`
**Y** el analista NO debe ver ninguna referencia al Modelo Tratamiento ni a sus probabilidades en el dashboard.

---

### Escenario: RF-01a — Aislamiento de Session State

**Dado** que el analista "analista_A" ingresó su ID y realizó una clasificación
**Cuando** la página del dashboard se recarga o se abre en una nueva pestaña
**Entonces** el campo `analista_id` debe aparecer vacío
**Y** el formulario de clasificación debe estar bloqueado hasta que se ingrese un nuevo ID.

---

### Escenario: CA-02b — Input de Frontera (Placeholder)

> **Nota:** Este escenario se completará con valores reales de sepal/petal tras el primer entrenamiento del modelo en Phase Modeling, basándose en la calibración del umbral 0.60 para que represente ≤15% del test set.

**Cuando** el analista ingresa los valores de frontera definidos en la calibración
**Entonces** el sistema debe activar el trigger de baja confianza (max prob < 0.60).
