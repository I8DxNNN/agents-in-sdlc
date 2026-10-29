---
title: "Ejercicio 1: Instrucciones personalizadas (VS Code)"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

[← Lección anterior: Requisitos previos][previous-lesson] · [Siguiente lección: Modo de agente →][next-lesson]

El contexto es fundamental cuando se trabaja con IA generativa. Si una tarea debe realizarse de una forma concreta o Copilot necesita conocer información de contexto, asegúrate de que tenga acceso a ella. Hay varias formas de compartir contexto específico con Copilot. Una de las principales son los [archivos de instrucciones][instruction-files], que proporcionan indicaciones sobre la generación de código.

En este ejercicio:

- explorarás cómo el contexto específico del proyecto, las directrices de programación y los estándares de documentación llegan a Copilot mediante instrucciones personalizadas del repositorio y archivos de instrucciones aplicables a rutas concretas;
- generarás la primera parte de la capa de datos para el filtrado (una función auxiliar de editoriales) con las instrucciones actuales;
- añadirás un nuevo estándar general a `.github/copilot-instructions.md`;
- volverás a ejecutar la misma solicitud y observarás cómo el código generado adopta el nuevo estándar;
- confirmarás los cambios de instrucciones y la parte de filtrado en `main` para que Copilot utilice las indicaciones actualizadas en el siguiente ejercicio.

> [!CAUTION]
> El código generado puede no cumplir algunos de los estándares que establezcas. Copilot no es determinista. El objetivo del ejercicio es observar la *tendencia* de los cambios en el comportamiento tras actualizar las instrucciones, no obtener siempre una salida idéntica.

## Archivos de instrucciones

### Situación

Como cualquier empresa de desarrollo, Tailspin Toys tiene directrices y requisitos para las prácticas de desarrollo. Entre ellos:

- la capa de datos siempre debe tener pruebas unitarias;
- la interfaz de usuario debe usar un tema oscuro y tener un aspecto moderno;
- el código debe incluir documentación en forma de comentarios TSDoc;
- cada archivo debe empezar con un bloque de comentarios que describa su función.

Con los archivos de instrucciones proporcionarás a Copilot el contexto adecuado para que realice las tareas conforme a estas prácticas.

### Instrucciones personalizadas

Las instrucciones personalizadas proporcionan contexto y preferencias a Copilot para que entienda mejor tu estilo de programación y tus requisitos. Esta función te permite orientar las sugerencias para que sean más pertinentes. Puedes especificar las convenciones de programación y las bibliotecas preferidas, además de los tipos de comentarios que quieres incluir. Puedes crear instrucciones para todo el repositorio o para tipos concretos de archivo y tareas.

Hay dos tipos de archivos de instrucciones:

- `.github/copilot-instructions.md` es un único archivo que se envía a Copilot en **todas** las solicitudes del repositorio. Debe contener información general del proyecto pertinente para la mayoría de las solicitudes, como la pila tecnológica, una descripción general del trabajo, las prácticas recomendadas y otras indicaciones globales.
- Los archivos `.github/instructions/*.instructions.md` se pueden crear para tareas o tipos de archivo concretos. Por ejemplo, sirven para definir instrucciones para lenguajes específicos (como TypeScript), crear componentes de interfaz de usuario o escribir pruebas unitarias.

> [!NOTE]
> En el IDE, los archivos de instrucciones solo se usan para generar código en Copilot Chat, no para completar código ni para las sugerencias de siguiente edición.
>
> Copilot Chat, Copilot CLI y el agente en la nube de Copilot utilizan tanto las instrucciones del repositorio como los archivos `*.instructions.md` que incluyen metadatos `applyTo`.
>
> [Copilot también admite archivos de instrucciones que siguen otros estándares][custom-instructions-support], como AGENTS.md y CLAUDE.md.

### Prácticas recomendadas para gestionar archivos de instrucciones

Explicar detalladamente cómo crear archivos de instrucciones queda fuera del alcance de este ejercicio. Los ejemplos del proyecto muestran un enfoque representativo. En general:

- Limita las instrucciones de `copilot-instructions.md` a indicaciones de nivel de proyecto, como una descripción de lo que se desarrolla, la estructura del proyecto y los estándares generales de programación.
- Usa archivos `*.instructions.md` para instrucciones específicas de tipos de archivo (pruebas unitarias, componentes de Astro o capa de datos) o de tareas.
- Usa lenguaje natural y presenta las indicaciones con claridad. Incluye ejemplos del aspecto que debe tener el código y del que no debe tener.

No existe una única forma de crear instrucciones o de usar IA. La experimentación te ayudará a encontrar el enfoque adecuado para tu proyecto.

> [!TIP]
> Todos los proyectos que usan GitHub Copilot deberían tener una colección sólida de archivos de instrucciones. Al explorar los archivos de este proyecto, puede que encuentres instrucciones para muchas tareas, como las [actualizaciones de la interfaz de usuario][ui-instructions] y [Astro][astro-instructions].
>
> Copilot también puede ayudarte a generar archivos de instrucciones. Cada interfaz ofrece esta opción de una forma distinta; por ejemplo, **Configure Chat → Generate Agent Instructions** en VS Code o `/init` en Copilot CLI. La lección correspondiente a cada interfaz lo explicará cuando sea pertinente.
>
> Si buscas plantillas o un punto de partida, explora [awesome-copilot][awesome-copilot], un repositorio con archivos de instrucciones, agentes personalizados y otros recursos.

[ui-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/ui.instructions.md
[astro-instructions]: https://github.com/github-samples/tailspin-toys/blob/main/.github/instructions/astro.instructions.md
[awesome-copilot]: https://github.com/github/awesome-copilot
[custom-instructions-support]: https://docs.github.com/copilot/reference/custom-instructions-support

## Explora los archivos de instrucciones personalizadas del proyecto

Dedica un momento a leer los archivos de instrucciones que incluye este repositorio: hay un archivo principal `copilot-instructions.md` y varios archivos `*.instructions.md` para distintas tareas. Ábrelos en el editor o en la interfaz web de GitHub.

1. Abre `.github/copilot-instructions.md`.
2. Explora el archivo y fíjate en la breve descripción del proyecto y en secciones como **Agent notes**, **Code standards**, **Scripts** y **Repository Structure**. En **Code standards**, observa las indicaciones anidadas sobre **GitHub Actions Workflows**. Se aplican a cualquier interacción con Copilot.
3. Abre la carpeta `.github/instructions` y explora su contenido. Verás instrucciones para archivos de Astro, la capa de datos de Drizzle, pruebas y otras tareas.
4. Abre `.github/instructions/unit-tests.instructions.md`. Observa el campo `applyTo` del principio: define un patrón glob (relativo a la raíz del repositorio) que determina los archivos a los que se aplican las instrucciones. En este caso, coincide con cualquier archivo de prueba de TypeScript, como los que siguen el patrón `**/*.test.ts`.
5. Observa las instrucciones específicas para crear pruebas unitarias en este proyecto.
6. Por último, abre `.github/instructions/drizzle.instructions.md` y desplázate hasta el final. Observa los enlaces a otros archivos de instrucciones (como `unit-tests.instructions.md`) y a archivos existentes del proyecto. Esto permite dividir conjuntos de instrucciones grandes en archivos más pequeños y reutilizables, y señalar a Copilot ejemplos que puede seguir. En ese archivo, las rutas son relativas al propio archivo de instrucciones, no a la raíz del repositorio.

> [!NOTE]
> La sección **Code formatting requirements** de `copilot-instructions.md` documenta los estándares de programación del proyecto, pero todavía no exige documentación dentro del código. En los pasos siguientes añadirás reglas para los comentarios TSDoc y los encabezados de comentarios de archivo.

## Crea una rama para los cambios

Sigamos algunas prácticas recomendadas y creemos una rama para los cambios.

1. Vuelve al codespace del ejercicio anterior.
2. Abre un terminal nuevo pulsando <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
3. Crea una rama y cámbiate a ella:

   ```bash
   git checkout -b custom-instructions
   ```

## Usa Copilot Chat *antes* de actualizar las instrucciones

> [!TIP]
> **Abre Copilot Chat**
>
> Antes de empezar los ejercicios siguientes, vuelve a tu codespace, abre el panel de Copilot Chat y selecciona **New Chat** para iniciar una conversación nueva. El modo y el modelo varían según el ejercicio; cada paso lo indicará cuando sea necesario.

Para observar el efecto de las instrucciones personalizadas, empieza enviando una solicitud con el archivo de instrucciones actual. Más adelante, lo actualizarás y volverás a enviar la misma solicitud para comparar los resultados.

1. Cierra las pestañas abiertas del editor de los ejercicios anteriores para que Copilot solo reciba el contexto que necesitas.
2. Abre `src/lib/publishers.ts` para indicar a Copilot dónde debe ubicarse la función auxiliar.
3. En el menú desplegable de agentes de la vista Chat, selecciona **Agent** para que Copilot pueda modificar archivos.

   ![Captura de pantalla del selector de agentes en la vista Chat](../../../_images/shared-chat-mode-selector.png)

4. Envía esta solicitud:

   ```plaintext
   Crea o actualiza src/lib/publishers.ts con una función auxiliar de acceso a datos que devuelva una lista de todas las editoriales, con su nombre e id. Aplica los cambios al archivo.
   ```

5. Copilot explorará el proyecto y aplicará cambios de código, a menudo tanto al archivo de la función auxiliar como a sus pruebas.
6. Observa que la función auxiliar propuesta está tipada, recibe un cliente `db` como primer argumento y devuelve una matriz tipada de editoriales. Esto se debe a las convenciones de la capa de datos en `.github/instructions/drizzle.instructions.md`, que se aplican a `src/lib/*.ts`.
7. Observa que al código propuesto **le faltan** comentarios TSDoc y un encabezado de comentario a nivel de archivo.

> [!CAUTION]
> Como Copilot no es determinista, puede que añada comentarios de documentación aunque no se le hayan pedido. Si ocurre, no pasa nada: el objetivo sigue siendo observar si la actualización de instrucciones mejora la coherencia.

## Añade un nuevo estándar del repositorio

Como se ha indicado, `.github/copilot-instructions.md` proporciona información general del proyecto a Copilot. Añadamos los estándares de programación para mejorar las sugerencias de código.

1. Vuelve a abrir `.github/copilot-instructions.md`.
2. Busca la sección **Code formatting requirements**, que debería estar cerca de la línea 27. Describe los estándares de programación del proyecto, pero todavía no incluye reglas para documentar el código; por eso la función auxiliar generada no tenía comentarios de documentación.
3. Añade estas líneas Markdown debajo de los estándares actuales para pedir comentarios de encabezado de archivo y comentarios TSDoc:

   ```markdown
   - Todas las funciones exportadas deben tener un comentario TSDoc que describa su propósito, sus parámetros y su valor devuelto.
   - Antes de las importaciones o de cualquier código, añade al archivo un bloque de comentarios que explique su propósito.
   ```

4. Guarda `copilot-instructions.md`.

> [!TIP]
> Como has visto en la lección anterior, puedes crear archivos de instrucciones en el repositorio (`.github/copilot-instructions.md`) para establecer directrices globales o como archivos `*.instructions.md` para lenguajes, tipos de archivo o tareas concretos. El archivo del repositorio es el lugar adecuado para estándares generales, como la regla sobre los comentarios de documentación.

## Vuelve a ejecutar la solicitud y observa el cambio

1. Vuelve a Copilot Chat y selecciona **New Chat** para borrar el contexto anterior.
2. Vuelve a seleccionar `src/lib/publishers.ts` para centrar Copilot en el archivo adecuado.
3. Envía la **misma solicitud** que antes:

   ```plaintext
   Crea o actualiza src/lib/publishers.ts con una función auxiliar de acceso a datos que devuelva una lista de todas las editoriales, con su nombre e id. Aplica los cambios al archivo.
   ```

4. Observa que el archivo propuesto ahora empieza con un bloque de comentarios parecido a este:

   ```typescript
   /**
    * Helpers de acceso a datos de editoriales para la plataforma de financiación colectiva de Tailspin Toys.
    * Proporciona funciones para recuperar información de editoriales de la base de datos.
    */
   ```

5. Observa que la función propuesta incluye un comentario TSDoc parecido a este:

   ```typescript
   /**
    * Devuelve una lista de todas las editoriales con su id y nombre.
    *
    * @param db - Cliente de base de datos de Drizzle.
    * @returns Una promesa que se resuelve en una matriz de objetos de editorial.
    */
   ```

Has orientado a Copilot para que siga un estándar nuevo y lo aplique al código con el que trabajarás en el ejercicio siguiente.

## Confirma las instrucciones y publica la rama

Los archivos de instrucciones son recursos del repositorio como cualquier otro y se gestionan mediante el mismo sistema de control de versiones. Confirmemos y publiquemos la rama.

1. Abre una ventana de terminal nueva pulsando <kbd>Control</kbd>+<kbd>\`</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>\`</kbd> (Windows/Linux).
2. Desde el terminal, comprueba que estén presentes la actualización de las instrucciones y los cambios de la función auxiliar:

   ```bash
   git status
   ```

3. Prepara y confirma la actualización de instrucciones y todos los archivos modificados por Copilot para crear la función auxiliar inicial:

   ```bash
   git add .github/copilot-instructions.md src/lib/publishers.ts
   # Si git status muestra actualizaciones adicionales (por ejemplo, pruebas), añade también esos archivos.
   git commit -m "Add doc comment standards and publishers helper foundation"
   ```

4. Publica la rama en el repositorio:

   ```bash
   git push -u origin custom-instructions
   ```

## Crea y combina una solicitud de cambios

Ya has publicado la rama. Ahora crea una solicitud de cambios y relaciónala con la incidencia sobre los estándares de documentación del backlog. Podrías hacerlo manualmente, pero Copilot puede utilizar las herramientas de GitHub que ya están conectadas al proyecto. Pídele que encuentre la incidencia, cree una PR relacionada y la combine.

> [!NOTE]
> Este es el primer ejercicio en el que Copilot actúa en GitHub. La plantilla del proyecto ya configura las herramientas de GitHub en el espacio de trabajo, así que no tienes que instalar nada. La primera vez que Copilot use una herramienta, VS Code puede pedirte que inicies sesión en GitHub. Sigue las instrucciones para autorizarlo. En un ejercicio posterior aprenderás cómo funciona esta conexión mediante Model Context Protocol.

1. Abre Copilot Chat en tu codespace.
2. Pulsa <kbd>Control</kbd>+<kbd>Command</kbd>+<kbd>I</kbd> (Mac) o <kbd>Ctrl</kbd>+<kbd>Alt</kbd>+<kbd>I</kbd> (Windows/Linux) para abrir la vista Copilot Chat y confirma que **Agent** está seleccionado.
3. Pide a Copilot que encuentre la incidencia sobre las instrucciones y cree una PR desde la rama actual, describiendo la actualización de instrucciones y la función auxiliar inicial de editoriales:

   ```plaintext
   Encuentra la incidencia relacionada con la actualización del archivo de instrucciones. Crea una solicitud de cambios desde la rama actual, indica que cierra esa incidencia y menciona también que hemos añadido una función auxiliar de editoriales para el próximo trabajo de filtrado.
   ```

4. Cuando Copilot te solicite **Allow** para realizar acciones en GitHub en tu nombre, revisa el comando y selecciona **Allow** si procede.
5. Cuando se cree la PR, pide a Copilot que la combine y vuelva a `main` en local con esta solicitud:

   ```plaintext
   Combina la PR en main. Después, vuelve a main en local y descarga el código más reciente para que esté actualizado.
   ```

6. Cuando Copilot te solicite **Allow** para realizar acciones en GitHub y ejecutar comandos de shell en tu nombre, revisa el comando y selecciona **Allow** si procede.

¡Has creado y combinado una solicitud de cambios con ayuda de GitHub Copilot!

## Resumen y siguientes pasos

Has explorado cómo Copilot utiliza los archivos de instrucciones de este proyecto y has usado Copilot Chat en VS Code para:

- enviar una solicitud de generación de código y observar el resultado con las instrucciones *existentes*;
- añadir un estándar general a `.github/copilot-instructions.md`;
- volver a enviar la misma solicitud y observar cómo el código propuesto adopta el estándar nuevo;
- confirmar en `main` la actualización de instrucciones y la función auxiliar inicial para que el siguiente ejercicio pueda aprovecharlas.

A continuación, aplicarás esas instrucciones en el [modo de agente][next-lesson] mientras Copilot añade una funcionalidad nueva en el código.

## Recursos

- [Archivos de instrucciones para personalizar GitHub Copilot][instruction-files]
- [Prácticas recomendadas para crear instrucciones personalizadas][instructions-best-practices]
- [Cinco consejos para redactar mejores instrucciones personalizadas para Copilot][copilot-instructions-five-tips]
- [Instrucciones personalizadas personales para GitHub Copilot][personal-instructions]
- [Awesome Copilot: colección de archivos de instrucciones y otros recursos][awesome-copilot]

[previous-lesson]: ../0-prerequisites/
[next-lesson]: ../2-agent-mode/
[instruction-files]: https://code.visualstudio.com/docs/copilot/copilot-customization
[instructions-best-practices]: https://docs.github.com/enterprise-cloud@latest/copilot/using-github-copilot/coding-agent/best-practices-for-using-copilot-to-work-on-tasks#adding-custom-instructions-to-your-repository
[personal-instructions]: https://docs.github.com/copilot/customizing-copilot/adding-personal-custom-instructions-for-github-copilot
[copilot-instructions-five-tips]: https://github.blog/ai-and-ml/github-copilot/5-tips-for-writing-better-custom-instructions-for-copilot/
