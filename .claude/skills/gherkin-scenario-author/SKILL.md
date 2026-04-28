---
name: gherkin-scenario-author
description: Protocolo para la traducción de User Stories aprobadas en escenarios BDD (Behavior-Driven Development) en formato Gherkin (Given/When/Then). Genera el Contrato de Comportamiento (behavior.md) con datos reales del dominio, cubriendo el camino feliz, casos de baja confianza y errores de rango. Activar tras la aprobación del BRD y antes del SpecDD.
---

# Gherkin Scenario Author

## Misión

Traducir las User Stories del BRD en especificaciones de comportamiento ejecutables (Gherkin), formando la capa BDD de la jerarquía metodológica:

```
BRD (Intención) → BDD/Gherkin (Comportamiento) → TDD (Corrección del código)
```

El output es el archivo `docs/governance/behavior.md`, que actúa como **Contrato de Comportamiento** vinculante y **Definition of Done (DoD) Absoluto** para todas las tareas de ingeniería. Los agentes de QA (`ai-data-qa-engineer`, `ai-full-stack-sdet`) usarán exclusivamente estos escenarios para escribir los tests que dictaminarán cuándo una tarea está terminada (GREEN).

---

## Protocolo de Ejecución

### Paso 1: Lectura de Prerrequisitos

Leer obligatoriamente antes de escribir un solo escenario:

1. `docs/governance/BRD.md` → Sección 8 (User Stories) y Sección 9 (Criterios de Aceptación)
2. `docs/governance/contract.md` → Rangos de validación de variables
3. `docs/governance/SpecDD.md` → Tipos de retorno de `PredictionResult` para verificar qué campos están disponibles para los pasos `Entonces`

### Paso 2: Clasificación de Escenarios por User Story

Para cada User Story, identificar y escribir obligatoriamente:

| Tipo de Escenario | Descripción | Obligatorio |
| :--- | :--- | :--- |
| **Camino Feliz (Happy Path)** | Datos válidos → predicción correcta con alta confianza | Sí, siempre |
| **Baja Confianza** | Datos válidos pero ambiguos → advertencia visible | Si la US lo menciona |
| **Error de Rango** | Datos fuera de rango → mensaje de error sin stack trace | Si la US lo menciona |
| **Caso Límite (Edge Case)** | Valores en el borde del rango permitido (mínimo y máximo) | Si aplica |

### Paso 3: Escritura de Escenarios Gherkin

Formato obligatorio por escenario:

```gherkin
Escenario: [Nombre descriptivo en lenguaje de negocio]
  Dado que [estado inicial del sistema o del usuario]
  Cuando [acción del usuario o evento]
  Entonces [resultado observable y verificable]
  Y [condición adicional si aplica]
```

**Reglas de Calidad (El BDD es el DoD Absoluto):**

- Al ser el BDD el único criterio de finalización de una tarea, los pasos `Entonces` deben describir un resultado exacto y medible que un test automatizado pueda evaluar como `GREEN` o `RED` sin dejar espacio a interpretaciones.
- Los valores en los pasos `Dado/Cuando` deben ser datos **reales del dominio** (ej: `petal_length: 1.4`, no `petal_length: <valor>`).
- Los pasos `Entonces` deben ser **verificables mecánicamente** (observable en el DOM de la UI o en la estructura JSON de respuesta de la API).
- El nombre del escenario usa lenguaje de negocio, no técnico. Prohibido: "ValidationError al ingresar float". Correcto: "Rechazo de medida de sépalo demasiado grande".
- Cada escenario referencia su `US-ID` de origen en un comentario o en el nombre del Feature.

### Paso 4: Validación de Cobertura

Antes de emitir el documento, verificar que se cumplen todos estos checks:

| Check | Criterio |
| :--- | :--- |
| Cobertura de User Stories | Cada US del BRD tiene al menos 1 escenario Happy Path |
| Cobertura de KPIs | Los umbrales técnicos del BRD (ej: confianza < 60%) están representados en al menos un escenario |
| Trazabilidad | Cada escenario referencia su US-ID de origen |
| Datos Reales | No hay placeholders (`<valor>`, `X`, `N`) en los pasos Gherkin |
| Verificabilidad | Cada paso `Entonces` puede ser automatizado sin interpretación adicional |
| Alineación con SpecDD | Los campos usados en los pasos `Entonces` existen en el tipo de retorno del módulo correspondiente |

### Paso 5: Emisión del Contrato

Escribir `docs/governance/behavior.md` con la estructura definida en el Formato de Salida.

---

## Formato de Salida: behavior.md

```markdown
# Behavior Specifications (BDD Contract)
## Proyecto: [Nombre del Proyecto]

> **Documento:** Contrato de Comportamiento BDD
> **Version:** X.Y.Z
> **Estado:** Aprobado
> **Fecha:** YYYY-MM-DD
> **Autor:** ai-business-strategist
> **Trazabilidad:** BRD vX → behavior.md vX → SpecDD vX
> **Fuente de verdad para:** ai-data-qa-engineer (Phase Engineering) · ai-full-stack-sdet (Phase Delivery)

---

## Jerarquía de Especificación

BRD (Intención) → BDD (Comportamiento) → TDD (Corrección)

---

## Feature: [Nombre de la Funcionalidad Principal]

### US-XX: [Nombre de la User Story]

```gherkin
Escenario: [nombre en lenguaje de negocio]
  Dado que ...
  Cuando ...
  Entonces ...
  Y ...
```

---

## Trazabilidad BDD → Criterios de Aceptación

| Escenario | US Origen | CA Relacionados |
| :--- | :--- | :--- |
| [nombre] | US-XX | CA-XX, CA-XX |

---

## Nota de Alineación con SpecDD

[Confirmar que los campos del tipo de retorno definido en SpecDD cubren todos los estados requeridos por los escenarios. Si hay gaps, documentarlos aquí como Change Control pendiente.]
```

---

## Disparador (Trigger)

- **Cuándo ejecutar:** Tras la aprobación del BRD (`docs/governance/BRD.md` con estado "Aprobado").
- **Posición en el flujo:** Después del BRD, antes de o en paralelo con el SpecDD.
- **Quién lo consume:**
  - `ai-data-qa-engineer` → escribe tests RED en Phase Engineering basándose en los escenarios.
  - `ai-full-stack-sdet` → crea E2E tests en Phase Delivery que validan cada escenario Gherkin.

---

## Entregable

`docs/governance/behavior.md` — Contrato de Comportamiento oficial del proyecto.


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
