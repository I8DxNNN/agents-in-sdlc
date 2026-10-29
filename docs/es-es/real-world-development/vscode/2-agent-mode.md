---
title: "Ejercicio 2: Añade funcionalidades con el modo de agente de Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

Incluso las actualizaciones más sencillas de una aplicación suelen requerir cambios en varios archivos y operaciones como ejecutar pruebas. El flujo habitual de un desarrollador consiste en localizar todos los archivos necesarios, hacer los cambios, ejecutar las pruebas, depurar, descubrir qué archivo se ha pasado por alto, hacer otra actualización... La lista continúa.

Para eso sirve el modo de agente de Copilot.

El modo de agente de Copilot está diseñado para trabajar con más autonomía en el IDE. Se comporta de forma parecida a un desarrollador: primero explora la estructura del proyecto, realiza los cambios necesarios, ejecuta tareas como las pruebas y corrige automáticamente los errores que detecta. Veamos cómo usar el modo de agente para añadir funcionalidades al sitio.

> [!NOTE]
> Aunque sus nombres se parecen, el modo de agente y el agente en la nube ofrecen experiencias distintas. El modo de agente realiza las tareas en el IDE, lo que permite ciclos de respuesta rápidos e interacción directa. El agente en la nube trabaja de forma asíncrona como un miembro del equipo y se comunica contigo mediante incidencias y solicitudes de cambios.

En este ejercicio aprenderás:

- cómo el modo de agente de Copilot explora el proyecto, identifica archivos pertinentes y coordina cambios;
- cómo el modo de agente de GitHub Copilot implementa funcionalidades en la interfaz de usuario y la capa de datos;
- cómo revisar los cambios y las pruebas generados por el modo de agente de Copilot antes de incorporarlos al código base.

## Situación

A medida que crece la lista de juegos, quieres permitir que la gente los filtre por categoría y editorial. En el ejercicio anterior ya añadiste una función auxiliar para las editoriales; ahora completarás con el modo de agente de Copilot el resto del trabajo de datos, interfaz y pruebas.

## Ejecuta el sitio web de Tailspin Toys

Antes de hacer cambios, exploremos el sitio web de Tailspin Toys para conocer sus funciones actuales.

El sitio es una plataforma de financiación colectiva de juegos de mesa con una temática para desarrolladores. Permite mostrar juegos y consultar sus detalles. Se trata de una única aplicación Astro que genera las páginas como HTML estático durante la compilación. Las páginas consultan directamente una base de datos SQLite local mediante Drizzle ORM en su frontmatter; no hay una API de backend independiente ni un framework de interfaz en el cliente. Las funciones auxiliares reutilizables de acceso a datos se encuentran en `src/lib/`, y la interactividad se implementa con scripts `<script>` pequeños y específicos de Astro que usan las API DOM estándar.

### Inicia el sitio web

Para facilitar la ejecución del sitio, hay una tarea `npm run dev` que inicia el único servidor de desarrollo de Astro. Puedes ejecutarla en tu GitHub Codespace:

1. Vuelve a tu codespace. Después del ejercicio anterior deberías estar en `main`.
2. Abre un terminal nuevo en el codespace pulsando <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
3. Actualiza `main` y crea una rama para el trabajo de filtrado:

   ```bash
   git checkout main
   git pull
   git checkout -b filtering-vscode
   ```

4. Ejecuta el comando siguiente para iniciar el sitio web:

   ```bash
   npm run dev
   ```

   Cuando el servidor de desarrollo de Astro esté listo, debería aparecer un mensaje parecido a este:

   ```bash
   🚀 Tailspin Toys is ready!
      Astro server: http://localhost:4321
      Press Ctrl-C to stop.
   ```

> [!NOTE]
> Si aparece un cuadro de diálogo para abrir un navegador con `http://localhost:4321`, ciérralo seleccionando **x**.

5. Abre el sitio manteniendo pulsada la tecla <kbd>Command</kbd> (Mac) o <kbd>Ctrl</kbd> (Windows/Linux) y seleccionando en el terminal la dirección del servidor Astro `http://localhost:4321`.

> [!NOTE]
> En un codespace, seleccionar el enlace localhost en el terminal del codespace te redirige automáticamente a `https://<your-codespace-name>-4321.app.github.dev/`. Es un túnel privado al codespace, donde ahora se ejecuta el servidor web.

### Explora el sitio web

Cuando el sitio esté en ejecución, podrás explorar sus funciones principales:

- **Página de inicio**: muestra una lista de juegos de mesa con sus títulos, imágenes y descripciones.
- **Página de detalles del juego**: al seleccionar un juego, se abre una página con más información, como el título, la descripción, la editorial y la categoría.

## Explora el backlog con Copilot

> [!TIP]
> **Abre Copilot Chat**
>
> Antes de empezar los ejercicios siguientes, vuelve a tu codespace, abre el panel de Copilot Chat y selecciona **New Chat** para iniciar una conversación nueva. El modo y el modelo varían según el ejercicio; cada paso lo indicará cuando sea necesario.

La implementación inicial del sitio funciona, pero queremos ampliarla. Empecemos por revisar el backlog. Al crear el proyecto a partir de la plantilla se generó automáticamente una lista de incidencias; pide a GitHub Copilot que te las muestre.

1. En el menú desplegable de agentes de la vista Chat, selecciona **Agent**. El agente planifica e implementa cambios de forma autónoma en varios archivos, ejecuta comandos de terminal e invoca herramientas.

   ![Captura de pantalla del selector de agentes en la vista Chat](../../../_images/shared-chat-mode-selector.png)

2. Selecciona **Claude Sonnet 4.5** en la lista de modelos disponibles.

> [!CAUTION]
> Los autores de este taller no están expresando preferencia por un modelo concreto. Durante su desarrollo usamos Claude Sonnet 4.5 y lo incluimos en las instrucciones para que las sugerencias fueran relativamente coherentes y ofrecieran una buena experiencia. Sin embargo, como los modelos de lenguaje grandes son probabilísticos, es normal que las sugerencias difieran de las indicadas en el taller.

> [!NOTE]
> Debido a la naturaleza probabilística de los modelos de lenguaje grandes, Copilot puede utilizar un comando MCP distinto y aun así completar la tarea.

3. Envía a Copilot esta solicitud sobre el backlog:

   ```plaintext
   Muéstrame el backlog de elementos de mi repositorio de GitHub. Ayúdame a priorizarlos según su utilidad para los usuarios.
   ```

4. Selecciona **Continue** para permitir que se ejecute el comando que enumera todas las incidencias.
5. Revisa la lista generada.

Copilot también habrá priorizado los elementos según los que considera más útiles para los usuarios.

## Revisa los archivos de instrucciones

Antes de pedir al agente que genere código, revisa el archivo de instrucciones que proporcionará contexto para su trabajo. Usarás el archivo de la [interfaz de usuario (UI)](https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md), que explica cómo añadir funciones al sitio web.

1. En tu codespace, abre `.github/instructions/ui.instructions.md`.
2. Revisa las directrices generales para añadir funciones, entre ellas:
   - una descripción general de la arquitectura;
   - principios de diseño de componentes, capacidad de prueba y accesibilidad;
   - enlaces a archivos de instrucciones para distintos tipos, como Astro, Tailwind CSS y Drizzle.

> [!TIP]
> Los archivos de instrucciones permiten enlazar a otros archivos de instrucciones y a archivos del proyecto. Las rutas son relativas a la ubicación del propio archivo. Así se pueden reutilizar ejemplos, dividir instrucciones complejas en partes manejables y proporcionar plantillas.

## Implementa el filtrado

Para completar el filtrado tendrás que realizar al menos tres cambios independientes en la aplicación:

- añadir o ajustar la lógica de filtrado restante en la capa de datos (`src/lib/`);
- añadir o actualizar las pruebas del comportamiento de filtrado;
- actualizar la página de listado de juegos para incluir la interfaz de filtrado.

Además, las pruebas deben ejecutarse y superarse antes de combinar los cambios en el código base. El modo de agente de Copilot puede encargarse de estas tareas. Añadamos la funcionalidad.

1. Puedes continuar en la conversación actual de Copilot o iniciar otra seleccionando **New Chat**.
2. Selecciona **Add Context**, **Instructions** y **ui** como archivo de instrucciones.

   ![Captura de pantalla de la selección del archivo de instrucciones de interfaz de usuario](../../../_images/ex3-select-instructions-file.png)

3. Confirma que **Agent** sigue seleccionado en el menú desplegable de agentes de la vista Chat.

   ![Captura de pantalla del selector de agentes en la vista Chat](../../../_images/shared-chat-mode-selector.png)

4. Confirma que sigue seleccionado el modelo **Claude Sonnet 4.5**.
5. Pide a Copilot que implemente la funcionalidad según la incidencia relacionada del backlog:

   ```plaintext
   Actualiza el sitio para incluir filtros por editorial y categoría conforme a los requisitos de la incidencia relacionada de GitHub en el backlog. Ya existe una función auxiliar para las editoriales del ejercicio anterior, así que consérvala y ajústala si hace falta al completar la lógica restante de la capa de datos, la interfaz y las pruebas. Asegúrate de que todas las pruebas se superen antes de terminar. El servidor ya está en ejecución; no hace falta iniciarlo.
   ```

6. Observa cómo Copilot explora el proyecto y localiza los archivos relacionados con la funcionalidad: las funciones auxiliares de datos, la interfaz y las pruebas. Después, empezará a modificar los archivos y a ejecutar las pruebas.

   ![Captura de pantalla de Copilot explorando los archivos del proyecto](../../../_images/ex3-agent-mode-explores.png)

> [!NOTE]
> Copilot realizará varias tareas, como explorar el proyecto, modificar archivos y ejecutar pruebas. Según la complejidad de la tarea y del código base, puede tardar unos minutos. Durante el proceso quizá veas aparecer los botones **Keep** y **Undo** en el editor. Al terminar, habrá un botón **Keep** o **Undo** para cada cambio; no tienes que seleccionar ninguno mientras el trabajo siga en curso.

7. Cuando Copilot te lo solicite, selecciona **Continue** para ejecutar las pruebas.

   ![Captura de pantalla de un cuadro de diálogo de Copilot Chat que pide confirmación para ejecutar pruebas](../../../_images/ex3-agent-mode-run-tests.png)

8. Puede que haya pausas e incluso que algunas pruebas fallen durante el proceso. No pasa nada: Copilot alterna entre generar código y ejecutar pruebas hasta completar la tarea y dejar de detectar errores.

   ![Captura de pantalla de una sesión completa de Copilot Chat con el modo de agente](../../../_images/ex3-agent-mode-proposed-changes.png)

9. Revisa el código generado para detectar posibles problemas.

> [!CAUTION]
> Recuerda que siempre es importante revisar el código generado por Copilot o por cualquier otra herramienta de IA.

10. Vuelve al navegador con el sitio en ejecución y explora la nueva funcionalidad.
11. Cuando hayas confirmado que todo funciona y revisado el código, selecciona **Keep** en la ventana de Copilot Chat.

## Resumen y siguientes pasos

¡Enhorabuena! En este ejercicio has aprendido a usar el modo de agente de GitHub Copilot para añadir funciones al sitio web de Tailspin Toys. Has aprendido:

- cómo implementa el modo de agente de GitHub Copilot funcionalidades en la interfaz de usuario y la capa de datos;
- cómo explora el proyecto, identifica archivos pertinentes y coordina cambios;
- cómo revisar los cambios y las pruebas generados por el modo de agente antes de incorporarlos al código base.

Ahora [probaremos la funcionalidad con el servidor MCP de Playwright][next-lesson] y abriremos una solicitud de cambios.

### Ejercicio de exploración adicional: implementa la paginación

A medida que crece la lista de juegos, será necesario habilitar la paginación. Utiliza las habilidades aprendidas en este ejercicio para pedir a Copilot que actualice el sitio. Al redactar la solicitud:

- sigue las prácticas recomendadas y utiliza los archivos de instrucciones existentes;
- decide cómo quieres implementar la paginación y si la persona usuaria podrá seleccionar el tamaño de página;
- proporciona a Copilot las indicaciones necesarias para crear la implementación que necesitas;
- puede que tengas que iterar, pedir cambios y proporcionar contexto; es normal al trabajar con Copilot.

## Recursos

- [Modos ask, edit y agent de Copilot: qué hacen y cuándo usarlos][choose-mode]
- [Modo de agente en VS Code][vs-code-agent-mode]

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-mcp/
[choose-mode]: https://github.blog/ai-and-ml/github-copilot/copilot-ask-edit-and-agent-modes-what-they-do-and-when-to-use-them/
[vs-code-agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
