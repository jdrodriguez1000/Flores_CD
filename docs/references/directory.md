# Project Directory Structure

Este documento define la estructura de carpetas y archivos obligatoria para el proyecto, asegurando la organización, trazabilidad y cumplimiento de los estándares de ingeniería de IA y ciencia de datos.

## Estructura Raíz

| Directorio | Propósito |
| :--- | :--- |
| `data/` | Almacenamiento de activos de datos en sus diferentes estados. |
| `docs/` | Documentación técnica, de negocio y de gobernanza. |
| `infra/` | Scripts de infraestructura como código (Docker, Terraform, K8s). |
| `models/` | Artefactos de modelos serializados y certificados. |
| `notebooks/` | Espacio de investigación, experimentación y prototipado. |
| `src/` | Código fuente productivo, modularizado y con tipado estricto. |
| `tests/` | Suite completa de pruebas técnicas y de calidad de modelos. |

---

## Detalle de Subdirectorios

### 1. Capa de Datos (`data/`)
Sigue la arquitectura Medallion para garantizar el linaje y la calidad:
- `data/Bronze/`: Datos crudos (raw) tal como se reciben de la fuente. Inmutables.
- `data/Silver/`: Datos limpios, normalizados y validados.
- `data/Gold/`: Tablas de características (features) listas para entrenamiento o consumo analítico.

### 2. Ecosistema de Documentación (`docs/`)
Segmentado por la fase del ciclo de vida y propósito administrativo:

- `docs/Phase_discovery/`: Factibilidad, mockups y entendimiento compartido.
- `docs/Phase_engineering/`: Reportes de EDAs, limpieza y transformación.
- `docs/Phase_modeling/`: Benchmarking, validación de modelos y reportes de QA de modelos.
- `docs/Phase_delivery/`: Certificados E2E y resultados de Stress Testing.

#### Gobernanza y Diseño (`docs/governance/` & `docs/design-system/`)
- `docs/governance/`: Documentos maestros (BRD, SAD, SpecDD, Behavior, Contract, Backlog).
- `docs/design-system/`: Fuente de verdad para la UI (tokens, tipografía, componentes).
- `docs/changes/`: Fichas formales de Control de Cambios (`CC-<ID>.md`).
- `docs/references/`: Guías operativas (`principles.md`, `config.md`, `agents.md`, `directory.md`).

### 3. Código Fuente (`src/`)
Modularizado según el Software Architecture Document (SAD):
- `src/data/`: Módulos de ingesta y transformación.
- `src/features/`: Lógica de generación de variables.
- `src/models/`: Scripts de entrenamiento e inferencia.
- `src/api/`: Definición de endpoints y contratos de entrada/salida.
- `src/core/`: Utilidades comunes, excepciones y configuraciones globales.

### 4. Suite de Pruebas (`tests/`)
- `tests/unit/`: Pruebas de funciones y módulos aislados.
- `tests/integration/`: Validación de flujo entre módulos.
- `tests/e2e/`: Pruebas de punta a punta basadas en `behavior.md`.
- `tests/model_qa/`: Validaciones de rendimiento y sesgo del modelo.

---

## Instrucciones de Construcción
1. El agente encargado de la inicialización debe crear esta estructura de forma recursiva.
2. Cada carpeta debe incluir un archivo `.gitkeep` si está vacía para asegurar su rastreo en el repositorio.
3. No se deben crear carpetas fuera de este estándar sin una ficha de Control de Cambios (CC) aprobada.
