# behavior.md — Contrato de Comportamiento (BDD)

> **Documento:** BDD Contract (Gherkin Scenarios)
> **Version:** 1.2.0
> **Estado:** Certificado (DoD Integral aplicado)
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

#### Escenario: RF-01b — Bloqueo por Inputs Fuera de Rango (Error de Usuario)
**Cuando** el analista ingresa "10.5" en `sepal_length` (fuera de rango 4.3-7.9)
**Entonces** el sistema debe mostrar un mensaje de error descriptivo en el campo
**Y** el botón "Clasificar" debe estar deshabilitado para prevenir el envío de datos inválidos.

---

### 2. Flujo del Analista 1 (Generación)

#### Escenario: US-01 — Clasificación con Alta Confianza (Camino Feliz A1)
**Cuando** el analista ingresa medidas válidas (ej. 5.1, 3.5, 1.4, 0.2)
**Y** presiona el botón "Clasificar"
**Entonces** el sistema debe mostrar la especie predicha "Iris setosa" con prob >= 0.60
**Y** la advertencia de baja confianza NO debe ser visible
**Cuando** el analista presiona el botón "Aceptar"
**Entonces** el sistema debe registrar en SQLite el estado `confirmada_a1` con `especie_analista1 = NULL`
**Y** el sistema debe generar un par de registros (control/shadow) con el mismo `prediction_batch_id`.

#### Escenario: US-02/CA-02a — Clasificación con Baja Confianza (Trigger Automático)
**Cuando** se ingresan medidas que retornan prob < 0.60 (Mock D-031: 0.45, 0.30, 0.25)
**Entonces** el sistema debe mostrar la especie predicha "Iris setosa"
**Y** debe mostrar la advertencia visual de baja confianza
**Cuando** el analista presiona el botón "Aceptar"
**Entonces** el sistema debe registrar en SQLite el estado `pendiente` con `especie_analista1 = NULL`.

#### Escenario: US-02 — Rechazo Manual del Analista 1 (Trigger Manual)
**Cuando** la predicción es "Iris virginica" pero el analista presiona "Rechazar"
**Y** selecciona "Iris versicolor" como especie correcta
**Entonces** el sistema debe registrar en SQLite el estado `pendiente` con `especie_analista1 = Iris versicolor`
**Y** el selector de especie al rechazar no debe permitir elegir la especie original "Iris virginica".

---

### 3. Flujo del Analista 2 (Revisión)

#### Escenario: RF-05 — Exclusión de Casos Propios en la Cola (Seguridad Operativa)
**Dado** que el analista "analista_gen" generó una predicción `pendiente`
**Cuando** el mismo analista "analista_gen" accede a la cola de revisiones
**Entonces** no debe ver su propio registro en la lista de casos por revisar.

#### Escenario: RF-05 — Visualización de Origen Combinado (Doble Trigger)
**Dado** que existe una predicción con `baja_confianza = True` Y `decision_analista1 = rechazada`
**Y** que el analista "analista_rev" (ID diferente) abre la cola
**Entonces** el origen del pendiente debe mostrarse como "baja_confianza_automatica + rechazo_analista1"
**Y** el botón de confirmación debe decir "Confirmar especie del Analista 1: [especie_elegida_por_A1]".

#### Escenario: US-03 — Acción de Confirmar/Corregir por A2 y Navegación
**Dado** que el analista "analista_rev" selecciona un caso de la cola
**Cuando** presiona "Confirmar"
**Entonces** el sistema debe mostrar "Caso cerrado correctamente"
**Y** el estado en SQLite debe pasar a `confirmada`
**Y** el analista debe permanecer en la vista de cola para procesar el resto de pendientes.
**Cuando** presiona "Corregir"
**Entonces** el sistema debe mostrar un selector con las 3 especies disponibles (sin filtros)
**Y** al elegir una, el estado en SQLite pasa a `corregida` registrando el veredicto final.

---

### 4. Shadow Testing e Integridad

#### Escenario: RF-08 — Shadow Testing (Ejecución Invisible)
**Cuando** el analista realiza cualquier clasificación
**Entonces** el sistema debe persistir un registro shadow con `is_shadow = True`
**Y** la interfaz de usuario no debe contener ninguna referencia visual al Modelo Tratamiento ni a sus probabilidades.

#### Escenario: RF-08 — Resiliencia ante Fallo del Modelo Tratamiento
**Dado** que el Modelo Tratamiento genera un error de runtime inesperado
**Cuando** el analista presiona "Clasificar"
**Entonces** el sistema debe mostrar la predicción del Modelo Control normalmente
**Y** el analista no debe recibir ninguna notificación del error (silencioso para el usuario)
**Y** en SQLite solo debe existir el registro del Modelo Control para ese batch.

---

## Matriz de Definición de Terminado (DoD) Integral

| Funcionalidad | Criterio de Aceptación (DoD) | Verificación Técnica (DoD) |
| :--- | :--- | :--- |
| **Ingreso (RF-01)** | Botón bloqueado ante valores fuera de rango. | Test unitario: `validate_form(inputs) == False`. |
| **Sesión (RF-01a)** | ID vacío tras F5 o cambio de pestaña. | Test E2E: Recarga de página limpia el input. |
| **A1 (RF-04)** | Registro SQLite con par Control/Shadow. | Auditoría DB: `count(prediction_batch_id) == 2`. |
| **Baja Confianza** | Advertencia visible si max(prob) < 0.60. | Mock: Inyección de `prob=0.45` activa CSS alert. |
| **Revisión (RF-05)** | Ocultamiento de registros del mismo `analista_id`. | Test de integración: Query con `WHERE user != current`. |
| **Corregir (RF-05)** | Selector A2 muestra todas las especies. | Verificación UI: Lista completa de 3 especies. |
| **A2 (RF-05)** | Transición a estados terminales. | Test DB: `estado IN ('confirmada', 'corregida')`. |
| **Navegación** | A2 se mantiene en la cola tras acción. | Test E2E: No hay redirección tras el mensaje de éxito. |
| **Shadow (RF-08)** | Join exacto entre modelos. | Auditoría DB: `control.UUID == shadow.UUID`. |
| **Resiliencia** | Fallo del shadow no detiene el control. | Inyección de `Exception` en shadow; control arroja `GREEN`. |
