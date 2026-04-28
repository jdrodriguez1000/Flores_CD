---
name: ui-ux-prototyping
description: Protocolo para la creación de interfaces premium no funcionales y validación de flujos de usuario (Mockups) con enfoque en Diseño de Autor.
user-invocable: false
agent: ai-ux-designer
allowed-tools: [generate_image, Read, Write, Edit, Bash]
---

# Skill: Prototipado UI/UX (Author Edition)

Esta habilidad permite al equipo validar la "cara" del proyecto antes de construir el "cerebro" (IA) utilizando técnicas de prototipado rápido y estética de alto impacto (Anti-AI Slop).

## Funciones

### 1. Dirección de Arte y Concepto (Art Direction)
**Acción:** `define_art_direction`
- Antes de prototipar, el agente debe definir una "Dirección de Arte" (ej: *Lujo Clínico, Brutalismo Técnico, Minimalismo Radical*).
- **Rechazo al "AI Slop":** Evitar layouts genéricos (sidebar izquierda + navbar arriba sin personalidad). Buscar asimetría y composiciones editoriales.
- Utiliza `generate_image` para crear conceptos visuales si la dirección es ambigua.

### 2. Construcción de Prototipo "Physical & Motion"
**Acción:** `build_author_prototype`
- Crea archivos HTML/CSS que se sientan "físicos" y vivos.
- **Texturizado:** Implementar sutiles ruidos de grano (`noise texture`) y gradientes de malla para romper la planitud digital.
- **Orquestación de Movimiento:** Usar animaciones CSS escalonadas (`staggered`) para que los elementos entren con ritmo, no todos a la vez.
- **Regla del Dato Quemado:** Datos estáticos (hardcoded) para máxima velocidad de iteración.

### 3. Mapeo de UI a Requerimientos
**Acción:** `map_ui_to_backlog`
- Traduce los componentes visuales aprobados en requerimientos técnicos para el **ai-frontend-engineer**.

## Reglas Técnicas
- **Design System as Foundation:** El `docs/design-system/` es el suelo, no el techo. Úsalo como base técnica (tokens), pero aplica la "Capa de Autor" encima.
- **Tipografía con Intención:** No usar fuentes estándar si el Design System no lo exige. Priorizar legibilidad con carácter.
- **Profundidad Real:** Evitar sombras de caja (`box-shadow`) genéricas. Usar capas tonales o sombras ambientales con gran difuminado (blur) y baja opacidad.
- **Velocidad sobre Perfección:** Un mockup al 80% visualmente excepcional es mejor que uno al 100% genérico.
- **Aislamiento:** El código del Mockup reside solo en `mockup/`.

---

## 🛡️ Mandatos de Gobernanza Global (Alineación process.md)
1. **BDD-as-DoD Absoluto:** Tu tarea no está terminada (DONE) ni lista para revisión humana hasta que el test automatizado asociado al escenario BDD arroje `GREEN`.
2. **Context Isolation:** Opera estrictamente bajo la regla de "Necesidad de Saber". Reclama solo tu archivo objetivo, tu prueba y tu fragmento del SpecDD. Rechaza contextos globales masivos.
3. **Tracer Bullets (Slices Verticales):** Ejecuta tu trabajo de manera iterativa por variable o funcionalidad específica (end-to-end). Está prohibido el desarrollo horizontal masivo.
