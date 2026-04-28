---
name: specdd-interface-definer
description: Protocolo para la definición técnica de interfaces de software (SpecDD) que garantiza la interoperabilidad modular y el cumplimiento de TDD.
user-invocable: false
agent: ai-solutions-architect
allowed-tools: [Read, Write, Edit]
---

## 🏗️ I. Definición de Contratos de Interfaz (Deep Modules)
Por cada módulo `.py` definido en el SAD, el agente debe establecer interfaces simples:
1. **Definición de Firmas Profundas:** Especificar nombres de funciones y parámetros. **Regla de Oro:** Minimizar la cantidad de parámetros expuestos. Encapsular la configuración compleja en objetos de estado o en la inicialización de la clase, para que la llamada a la función sea trivial (ej: `modelo.entrenar(datos)` en lugar de pasar 15 hiperparámetros).
2. **Manejo de Excepciones:** Definir qué errores debe lanzar cada componente y cómo deben ser capturados por la capa superior.
3. **Contratos de Comunicación:** Establecer cómo se comunicará la Phase Engineering con la 3 (ej: archivos Parquet) y la 3 con la 4 (ej: JSON via FastAPI).
4. **Documentación Obligatoria:** El SpecDD debe guardarse en `docs/governance/specdd.md`.

## 📐 II. Protocolo SpecDD (Specification-Driven Development)
Antes de que un ingeniero comience el desarrollo, el Architect debe entregar el "blueprint":
1. **Interfaces de Entrada:** Datos exactos que el módulo espera recibir.
2. **Post-condiciones:** El estado garantizado del sistema tras la ejecución del módulo.
3. **Comportamiento Mock:** Instrucciones sobre cómo simular (mockear) el módulo para que otros agentes puedan trabajar en paralelo sin dependencias reales.

## 🚀 III. Estándares de Ingeniería y Calidad
1. **Linting & Formatting:** Definir las reglas de estilo (ej: PEP 8, Black, Isort).
2. **Estructura de Logs:** Establecer el formato de telemetría (mínimo: timestamp, level, module, message, metadata).
3. **Puntos de Inyección:** Diseñar cómo se inyectarán las dependencias (ej: modelos, conexiones a DB) para facilitar las pruebas unitarias.

---

> **Check de Certificación SpecDD:**
> - [ ] ¿Cada archivo .py tiene definida su interfaz antes de ser creado?
> - [ ] ¿Las definiciones permiten que el agente de QA escriba los tests sin ver el código interno?
> - [ ] ¿Se han definido tipos personalizados (TypedDict, Pydantic) para datos complejos?


---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
