# PRINCIPIOS DE INGENIERÍA:

## 1. Pensar antes de programar

**No asumas. No ocultes la confusión. Debes exponer los pros y contras.**

Este principio obliga a un razonamiento explícito, para que no elijas una interpretación en silencio y 
avances con ella:

* **Declara tus suposiciones explícitamente:** Si no tienes certeza en cualquier punto del desarrollo, pregunta en lugar de adivinar.
* **Presenta múltiples interpretaciones:** No elijas en silencio cuando exista ambigüedad.
* **Cuestiona cuando sea justificado:** Si existe un enfoque más simple, dilo.
* **Detente cuando estés confundido:** Identifica qué no está claro y solicita aclaraciones.


## 2. Simplicidad Primero (Módulos Profundos)

**El código mínimo que resuelva el problema a través de interfaces simples.**

Combate la tendencia hacia la sobreingeniería:

* **Interfaces Simples, Complejidad Oculta:** Diseña módulos profundos. La interfaz debe ser fácil de usar, ocultando la complejidad interna. Evita funciones que requieran decenas de parámetros.
* **Sin funciones extra:** No añadas nada más allá de lo solicitado.
* **Sin abstracciones innecesarias:** No crees abstracciones para código de un solo uso.
* **Sin flexibilidad injustificada:** No añadas "configurabilidad" o flexibilidad que no haya sido pedida.
* **Optimización de volumen:** Si 200 líneas pueden ser 50, reescríbelo para facilitar la revisión y reducir la carga cognitiva.

**La prueba de fuego:** ¿Diría un ingeniero senior que la interfaz de este módulo es demasiado complicada? Si la respuesta es sí, simplifica.


## 3. Cambios Quirúrgicos y Aislamiento

**Toca solo lo que debas. Opera bajo el principio de "Necesidad de Saber".**

Al editar código existente o agregar nuevas características:

* **Aísla tu contexto:** Trabaja únicamente con los archivos y el contexto estrictamente necesarios para tu tarea actual. Evita procesar o intentar comprender la totalidad del sistema de golpe.
* **No "mejores" el entorno:** No alteres código adyacente, comentarios o formatos que no tengan que ver con la tarea.
* **No refactorices lo que no esté roto:** Si funciona y no es parte del objetivo, no lo toques.
* **Respeta el estilo existente:** Mantén la consistencia con el código actual, incluso si tú lo harías de otra forma.

**Cuando tus cambios generen residuos:**
* **Limpieza propia:** Elimina importaciones, variables o funciones que se hayan vuelto innecesarias debido a TUS cambios.
* **No elimines residuos preexistentes:** No borres código muerto antiguo a menos que se te pida explícitamente.


## 4. Desarrollo Incremental (Slices Verticales)

**Construye integración, no solo aislamiento funcional.**

Abandona la construcción en bloques horizontales masivos:

* **De punta a punta:** Construye funcionalidades completas (desde los datos hasta la interfaz) una a la vez. 
* **Verifica la conexión:** Asegúrate de que los componentes interactúan correctamente en un flujo delgado ("Tracer Bullet") antes de ensanchar el alcance funcional.


## 5. Ejecución Orientada a Objetivos y Comportamiento

**Define criterios de éxito. La tarea termina cuando la prueba pasa.**

Transforma tareas imperativas en objetivos verificables por comportamiento (BDD/TDD):

| En lugar de...     | Transforma a...                                          |
| :----------------- | :------------------------------------------------------- |
| "Añade validación" | "Escribe tests para el comportamiento de error y haz que pasen (GREEN)" |
| "Corrige el error" | "Escribe un test que lo reproduzca y luego haz que pase" |
| "Refactoriza X"    | "Asegúrate de que los tests pasen antes y después"       |

**Para tareas de varios pasos, define un plan breve:**
1. [Comportamiento esperado] → verificar: [test automatizado]
2. [Paso técnico] → verificar: [comprobación]

**La Regla de Oro:** La Definición de Terminado (Definition of Done) no es subjetiva. Si la prueba que rige el comportamiento no ejecuta con éxito, el código no está listo.