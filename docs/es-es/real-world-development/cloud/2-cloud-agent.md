---
title: "Ejercicio 2: Agente en la nube de GitHub Copilot"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lección anterior: Instrucciones personalizadas][previous-lesson] |
|:--|

Es probable que pocas organizaciones, si es que hay alguna, no tengan problemas de deuda técnica. Puede tratarse de problemas de seguridad sin resolver, código heredado que necesita actualizarse o solicitudes de funcionalidades que llevan tiempo en la lista de tareas porque nunca ha habido tiempo para implementarlas. El agente en la nube de GitHub Copilot está diseñado para realizar tareas como actualizar código y añadir funcionalidades de forma autónoma. Cuando termina, genera un borrador de solicitud de cambios (PR) para que lo revise una persona. Así se pueden delegar tareas tediosas, acelerar el desarrollo y permitir que los desarrolladores se centren en cuestiones de mayor alcance.

Con el agente en la nube de Copilot aprenderás a:

- personalizar el entorno de generación de código;
- asegurarte de que las operaciones se realicen de forma segura;
- valorar la importancia de delimitar claramente las incidencias;
- asignar incidencias a Copilot.

## Situaciones

Tailspin Toys tiene deuda técnica que quiere resolver. Los contratistas que crearon la primera versión del sitio dejaron la documentación en muy mal estado: de hecho, comprobarás que no hay ninguna. Como primer paso, quieren añadir comentarios TSDoc a todas las funciones exportadas de la aplicación.

Además, el equipo de diseño quiere mejorar cómo se descubren los juegos. Quiere mostrar juegos relacionados —otros títulos de la misma categoría— en la página de detalles de cada juego para que la gente pueda seguir explorando. No necesita una implementación pulida todavía; solo algo que permita hacer pruebas de aceptación de la experiencia de usuario. Aunque esto bloquea el trabajo actual, hay otras incidencias con mayor prioridad.

Estas son tareas que suelen aplazarse con facilidad y que son buenas candidatas para asignar al agente en la nube de Copilot. El agente puede trabajar en ellas de forma asíncrona mientras el desarrollador se ocupa de otras tareas y, después, revisar su trabajo y confirmar que todo está como se esperaba.

## Introducción al agente en la nube de GitHub Copilot

El [agente en la nube de GitHub Copilot](https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent#overview-of-copilot-cloud-agent-formerly-copilot-coding-agent) puede realizar tareas en segundo plano de forma similar a un desarrollador. Al igual que cuando trabajas con una persona, hay varias formas de asignarle trabajo, como [asignar una incidencia de GitHub a Copilot](https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions). Una vez asignada, Copilot creará un borrador de solicitud de cambios para registrar el progreso, configurará un entorno y empezará a trabajar. Puedes consultar la sesión mientras sigue en curso o después de que termine. Cuando esté lista para que revises la solución propuesta, te mencionará en la solicitud de cambios.

## La importancia de delimitar bien las instrucciones

GitHub Copilot no hace magia: no puedes saltarte el trabajo de pensar. Incluso las tareas que parecen sencillas pueden ser complejas cuando las analizas en detalle; por eso, [delimita con cuidado las tareas para el agente en la nube de Copilot][cloud-agent-best-practices]. Trátalo como a un compañero de programación con IA: trabaja por etapas, aprende, experimenta y adapta el enfoque sobre la marcha. Los fundamentos del desarrollo de software no cambian con la llegada de la IA generativa.

## Instrucciones personalizadas en este repositorio

En ejercicios anteriores aprendiste qué son las instrucciones personalizadas y cómo orientan a Copilot. Si ya completaste el ejercicio práctico sobre instrucciones personalizadas, habrás visto funcionar `.github/copilot-instructions.md`; si no, este es un buen momento para echarle un vistazo. Antes de asignar trabajo al agente en la nube, revisa los archivos de instrucciones que ya incluye este repositorio para que después puedas observar su efecto.

Abre estos archivos en la interfaz web de GitHub o en un codespace que ya tengas en ejecución:

- `.github/copilot-instructions.md`: revisa la sección **Code standards**, especialmente las convenciones de TypeScript y los comentarios de documentación TSDoc.
- `.github/instructions/unit-tests.instructions.md`: observa los metadatos `applyTo`, que aplican las instrucciones a los archivos `**/*.test.ts`.

Cuando asignes al agente en la nube la incidencia `Code lacks documentation` en la sección siguiente, fíjate en los comentarios TSDoc, las convenciones de TypeScript y los encabezados de comentarios que aparezcan en la PR. Estas pautas proceden de los archivos de instrucciones. Volveremos a señalarlas al revisar las PR en un ejercicio posterior.

## Configura el entorno de desarrollo del agente en la nube de Copilot

Crear código suele requerir un entorno específico y la ejecución de algunos scripts de configuración para que todo funcione correctamente. Lo mismo ocurre al asignar tareas a Copilot, que trabaja de forma parecida a un ingeniero de software.

El agente en la nube usa [GitHub Actions][github-actions] como entorno de trabajo. Puedes personalizarlo mediante un [flujo de configuración especial][setup-workflow], definido en `.github/workflows/copilot-setup-steps.yml`, que se ejecuta antes de que empiece el trabajo. Así se le proporcionan las herramientas de desarrollo y dependencias necesarias. Este flujo ya está configurado para el laboratorio: instala Node.js, las dependencias del proyecto y los binarios del navegador para las pruebas de extremo a extremo, y configura la base de datos SQLite migrada y precargada de la única aplicación Astro:

```yaml
name: "Copilot Setup Steps"

# Permite probar los pasos de configuración desde la pestaña "Actions" del repositorio
on: workflow_dispatch

env:
  ASTRO_TELEMETRY_DISABLED: "1"

jobs:
  copilot-setup-steps:
    runs-on: ubuntu-latest
    # Establece los permisos mínimos necesarios para *tus pasos*. Copilot recibirá su propio token para sus operaciones.
    permissions:
      # Para clonar el repositorio como parte de la configuración (por ejemplo, para instalar dependencias), necesitarás el permiso `contents: read`. Si no lo clonas en estos pasos, Copilot lo hará automáticamente cuando terminen.
      contents: read
    steps:
      - name: Checkout code
        uses: actions/checkout@v5

      # Configuración de frontend y aplicación: Node.js (la aplicación completa usa Astro + Drizzle/libSQL)
      - name: Set up Node.js
        uses: actions/setup-node@v6
        with:
          node-version: "lts/*"
          cache: "npm"
          cache-dependency-path: "./package-lock.json"

      - name: Install JavaScript dependencies
        run: npm ci

      - name: Install Playwright browsers
        run: npx playwright install --with-deps chromium

      # Migra y precarga la base de datos SQLite local para que las compilaciones y pruebas tengan datos.
      - name: Set up the database
        run: npm run db:setup
```

El archivo se parece a cualquier otro flujo de trabajo de GitHub, pero hay algunos detalles importantes:

- Contiene un único trabajo llamado `copilot-setup-steps`. GitHub Actions lo ejecuta antes de que Copilot empiece a trabajar en la solicitud de cambios.
- El activador `workflow_dispatch` permite ejecutar manualmente el flujo desde la pestaña **Actions** del repositorio. Así puedes comprobar que funciona sin esperar a que lo ejecute Copilot.

## Añade documentación

Aunque todo el mundo reconoce la importancia de la documentación, muchos proyectos tienen información obsoleta o carecen de ella. Esta deuda técnica suele quedar desatendida, lo que reduce la productividad y dificulta mantener el código o incorporar a nuevos desarrolladores. Copilot destaca en la generación de documentación, así que esta es una buena tarea para asignarle al agente en la nube. Trabajará en segundo plano y volverás a revisar su documentación en un ejercicio posterior.

1. Abre una pestaña nueva del navegador y ve a tu repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el cuadro de diálogo de una incidencia nueva.
4. Selecciona **Blank issue** para crear una incidencia en blanco.
5. Escribe `El código carece de documentación` en **Title**.
6. Escribe lo siguiente en **Description**:

    ```plaintext
    Nuestra organización requiere que las funciones y los métodos incluyan comentarios de documentación TSDoc cuando resulte útil. Sin embargo, las actualizaciones recientes no han seguido este estándar. Tenemos que actualizar el código existente para incluir comentarios de documentación que aclaren el comportamiento.
    ```

7. Selecciona **Create** para crear la incidencia.
8. A la derecha, selecciona **Assign to Copilot** para abrir el cuadro de diálogo de asignación.

    ![Asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

    ![Detalles de asignación de Copilot](../../../_images/ex4-assign-copilot-details.png)

10. Selecciona la pestaña **Pull Requests**.
11. Abre la solicitud de cambios (PR) que se acaba de generar; tendrá un título similar a `[WIP]: El código carece de documentación`. Si aún no aparece en la lista, espera un momento y actualiza la página.
12. Al cabo de unos minutos, Copilot debería haber creado una lista de tareas.

> [!NOTE]
> La lista de tareas de Copilot puede tardar varios minutos en aparecer en la PR. Copilot está creando su entorno (ejecutando el flujo de trabajo descrito antes), analizando el proyecto y determinando el mejor enfoque.

13. Revisa la lista y las tareas que va a completar.
14. Desplázate hacia abajo en la cronología de la solicitud de cambios. Deberías ver una actualización que indica que Copilot ha empezado a trabajar en la incidencia.
15. Selecciona el botón **View session**.

    ![Vista de la sesión de Copilot](../../../_images/ex4-view-session.png)

> [!CAUTION]
> Es posible que debas actualizar la ventana para ver el indicador actualizado.

16. Observa la sesión en directo para ver cómo Copilot resuelve el problema: explora el código y comprende su estado, se detiene a pensar y decidir un plan adecuado y, después, crea código.

El proceso probablemente tardará varios minutos. Uno de los objetivos principales del agente en la nube de Copilot es trabajar de forma asíncrona y permitirnos ocuparnos de otras tareas. Aprovecharemos esa función: asignaremos otra tarea al agente en la nube y, mientras tanto, nos ocuparemos de escribir código para añadir funcionalidades a la aplicación.

## Añade una sección de juegos relacionados a la página de detalles

Una de las ventajas del agente en la nube de GitHub Copilot es que permite dividir el trabajo: tú puedes centrarte en unas tareas mientras el agente se ocupa de otras. Aunque añadir una sección de juegos relacionados para el equipo de diseño no debería llevar mucho tiempo, podrías aprovechar ese tiempo para otras tareas. ¡Asignemos el trabajo al agente en la nube de Copilot!

1. Vuelve al repositorio en github.com.
2. Selecciona la pestaña **Issues**.
3. Selecciona **New issue** para abrir el cuadro de diálogo de una incidencia nueva.
4. Selecciona **Blank issue** para usar la plantilla en blanco.
5. Escribe `Mostrar juegos relacionados en la página de detalles del juego` en **Title**.
6. Escribe lo siguiente en **Description**:

    ```markdown
    Queremos ayudar a descubrir más juegos mostrando juegos relacionados en la página de detalles de cada juego. El equipo de diseño quiere explorar la experiencia y realizar pruebas de aceptación. Estos son los requisitos:

   - Añadir una función auxiliar de acceso a datos en `src/lib/` que devuelva otros juegos de la misma categoría que un juego determinado, sin incluir ese juego
   - Mostrar en la página de detalles del juego una sección «Juegos relacionados» que utilice la función auxiliar
   - Gestionar el caso en que un juego no tenga juegos relacionados
   - Crear pruebas unitarias para la nueva función auxiliar
   - Asegurarse de que todas las pruebas se superen antes de crear la PR
    ```

7. Selecciona **Create** para crear la incidencia.
8. A la derecha, selecciona **Assign to Copilot** para abrir el cuadro de diálogo de asignación.

    ![Asignación de Copilot a una incidencia](../../../_images/shared-assign-copilot.png)

9. Selecciona **Assign**.

Poco después, deberías ver varios 👀 en el primer comentario de la incidencia. Indican que Copilot ya está trabajando.

![Copilot utiliza el emoji de ojos para indicar que está trabajando en la incidencia](../../../_images/ex4-issue-eyes-emoji.png)

Copilot ya está trabajando en tu segunda solicitud. El agente en la nube funciona de forma parecida a un ingeniero de software: no hace falta supervisarlo continuamente, basta con revisar el resultado cuando termine. Ahora vamos a crear y utilizar agentes personalizados.

## Resumen y siguientes pasos

En esta lección has explorado el [agente en la nube de GitHub Copilot][copilot-agents]. Puedes asignarle incidencias para que las resuelva de forma asíncrona. Copilot puede ocuparse de deuda técnica, crear funcionalidades o ayudar a migrar código de un framework a otro.

Has aprendido a:

- personalizar el entorno de generación de código;
- asegurarte de que las operaciones se realicen de forma segura;
- valorar la importancia de delimitar claramente las incidencias;
- asignar incidencias a Copilot.

Mientras el agente en la nube trabaja, puedes crear y utilizar agentes personalizados. El [agente en la nube de Copilot también puede utilizar servidores MCP][cloud-agent-mcp] y las instrucciones personalizadas que exploramos en módulos anteriores.

## Recursos

- [Acerca del agente en la nube de Copilot][copilot-agents]
- [Asigna incidencias de GitHub a Copilot][assign-issue]
- [Prácticas recomendadas para el flujo de configuración del agente en la nube de Copilot][cloud-agent-best-practices]

| [Siguiente lección: Agentes personalizados →][next-lesson] |
|--:|

[previous-lesson]: ../1-custom-instructions/
[next-lesson]: ../3-custom-agents/
[cloud-agent-mcp]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/extend-cloud-agent-with-mcp
[assign-issue]: https://docs.github.com/copilot/how-tos/use-copilot-agents/cloud-agent/start-copilot-sessions
[setup-workflow]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[copilot-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-cloud-agent
[cloud-agent-best-practices]: https://docs.github.com/copilot/how-tos/copilot-on-github/customize-copilot/customize-cloud-agent/customize-the-agent-environment
[github-actions]: https://docs.github.com/actions
