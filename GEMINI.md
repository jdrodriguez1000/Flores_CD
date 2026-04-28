# Protocolo de Sincronización de Agentes

## Instrucción Crítica de Contexto
Antes de realizar cualquier acción, modificar código o actualizar el estado de las tareas, **ES OBLIGATORIO** leer y procesar el contenido del archivo `CLAUDE.md`.

## Razonamiento
1. **Fuente de Verdad:** `CLAUDE.md` contiene las definiciones maestras de los estados de las tareas, incluyendo la lógica de transición entre "Completada" y "Finalizada".
2. **Interoperabilidad:** Para garantizar que el trabajo realizado por otros agentes sea compatible con tus procesos, debes seguir las reglas de integración y validación descritas en dicho archivo.
3. **Validación de Iteración:** No marques una tarea como "Finalizada" sin antes verificar los criterios de éxito globales definidos en el documento compartido.

## Acción Inmediata
- Ejecuta un comando de lectura sobre `CLAUDE.md` ahora.
- Si encuentras discrepancias entre mis instrucciones locales y las de `CLAUDE.md`, prioriza siempre las definidas en `CLAUDE.md` para mantener la integridad de la iteración.