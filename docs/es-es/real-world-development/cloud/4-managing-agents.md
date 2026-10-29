---
title: "Ejercicio 4: Supervisar y gestionar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

En los dos ejercicios anteriores pediste a Copilot que se encargara de tres tareas para mejorar la experiencia de usuario y añadir funcionalidades. Aunque el agente en la nube está diseñado para trabajar de forma asíncrona y autónoma, sigue siendo importante poder supervisar esas tareas.

Hay varias herramientas para gestionar las tareas asignadas al agente en la nube, como la [página de agentes][agents-page] de GitHub.com. Desde este centro de control puedes ver todas las tareas de los agentes que tienen solicitudes de cambios abiertas. También puedes explorar las operaciones que han realizado e incluso guiar una sesión en curso.

En este ejercicio:

- explorarás la página de agentes para supervisar las tareas del agente en la nube;
- guiarás una sesión en curso para solicitar funcionalidad adicional.

## Situación

Después de asignar al agente la tarea de crear un modo de alto contraste, el equipo se dio cuenta de que también sería un buen momento para añadir un modo claro. Como ya se estaba trabajando en actualizar los estilos del sitio y añadir la funcionalidad de alternancia, parecía lógico incluir este modo. Quieres guiar el trabajo del agente para asegurarte de que añada tanto el modo claro como el modo de alto contraste.

## Revisa las tareas del agente en la nube de Copilot

Consulta el estado actual de las tareas asignadas al agente en la nube de Copilot:

1. Ve a la [página de agentes de Copilot](https://github.com/copilot/agents).
2. Observa la lista de tareas, tanto en el panel principal como en el panel izquierdo. Deberías ver las tareas que has asignado a Copilot, entre ellas:
   - actualizar la documentación de la base de código;
   - generar API para modificar juegos;
   - añadir un modo de alto contraste al sitio web.
3. Selecciona una de las tareas en curso. Revisa los pasos que ha realizado Copilot, que pueden incluir:
   - obtener el código del repositorio;
   - crear el entorno de trabajo de Copilot;
   - configurar servidores MCP;
   - realizar distintas tareas para completar el trabajo asignado.

> [!NOTE]
> Los pasos exactos variarán según el estado del trabajo de Copilot y el enfoque que haya seguido.

4. Observa también el panel de solicitudes de cambios que aparece a la derecha. Te permite ver la solicitud y los archivos modificados para seguir el trabajo.

## Guía al agente en la nube

Ahora que has visto las tareas activas, pide a Copilot que añada el modo claro mientras trabaja en el modo de alto contraste.

1. Selecciona la sesión relacionada con el modo de alto contraste. El título exacto dependerá del nombre que utilice Copilot y del estado actual del trabajo.

    ![Sesión de accesibilidad en el centro de control](../../../_images/ex6-accessibility-session.png)

2. Observa la sesión durante unos minutos, hasta que indique que ha terminado la configuración y ha empezado a trabajar. Lo notarás cuando aparezcan mensajes similares a los siguientes.
3. En el cuadro de diálogo **Steer active session while Copilot is working**, escribe esta solicitud:

    ```plaintext
    Mientras trabajamos en un modo de alto contraste, añadamos también un modo claro. Debe haber un control para este modo, de forma que las personas puedan seleccionar el modo de visualización que prefieran.
    ```

    ![Captura de pantalla de la tarea del agente en la nube en la página de agentes, con el cuadro de diálogo para guiar la sesión activa de Copilot resaltado](../../../_images/ex6-steer-cloud-agent-task.png)

4. Pulsa <kbd>Enter</kbd> para enviar la solicitud.
5. Observa cómo Copilot la confirma y la incorpora al trabajo.

## Deja que Copilot trabaje

¡Copilot empezará a trabajar en la tarea actualizada! Incorporará la nueva solicitud al flujo de trabajo cuando termine el paso en el que estaba trabajando al enviar el mensaje.

Como antes, el proceso tardará varios minutos. Es un buen momento para hacer una pausa y reflexionar sobre todo lo que has aprendido y explorado hasta ahora.

## Resumen y siguientes pasos

En este ejercicio has explorado la página de agentes de Copilot, el centro de control para supervisar y guiar las tareas del agente en la nube de GitHub Copilot. Desde allí puedes seguir las tareas activas y completadas, revisar el trabajo que se está realizando y redirigir las sesiones en curso para ajustar su alcance o proporcionar más indicaciones.

Has aprendido a:

- explorar el centro de control y la página de agentes para supervisar las tareas del agente en la nube;
- redirigir una sesión en curso para solicitar funcionalidad adicional.

Mientras Copilot termina de trabajar en las funciones de accesibilidad, puedes [iterar sobre las solicitudes de cambios creadas por Copilot][next-lesson].

## Recursos

- [Página de agentes de Copilot][agents-page]
- [Acerca de los agentes personalizados][custom-agents]

---

| [← Lección anterior: Agentes personalizados][previous-lesson] | [Siguiente lección: Iterar sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../3-custom-agents/
[next-lesson]: ../5-iterating/
[agents-page]: https://github.blog/changelog/2025-07-03-agents-page-for-copilot-coding-agent-in-public-preview
[custom-agents]: https://docs.github.com/copilot/concepts/agents/cloud-agent/about-custom-agents
