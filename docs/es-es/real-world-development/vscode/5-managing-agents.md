---
title: "Ejercicio 5: Supervisar y gestionar agentes"
authors:
  - geektrainer
lastUpdated: 2026-06-30
---

| [← Lección anterior: Agentes personalizados][previous-lesson] |
|:--|

Cuando pones GitHub Copilot en modo de agente, trabaja de forma autónoma: explora el código base, propone cambios, edita archivos y ejecuta comandos. Como trabaja en tu equipo en tiempo real, Copilot Chat en VS Code muestra las llamadas a herramientas y las ediciones de archivos a medida que se producen. Puedes revisar cada diferencia en línea, aceptar o rechazar cambios concretos y guiar la conversación con solicitudes de seguimiento para refinar o ampliar el trabajo sin salir del editor.

En este ejercicio:

- supervisarás en tiempo real el trabajo del agente de accesibilidad;
- revisarás y aceptarás las ediciones de archivos propuestas;
- guiarás al agente durante la sesión para añadir un control del modo claro además del modo de alto contraste.

## Situación

El agente personalizado de accesibilidad que usaste en el [ejercicio 4][previous-lesson] está implementando el modo de alto contraste. Mientras trabaja, el equipo de diseño solicita además un control de modo claro para quienes encuentran demasiado oscuro el tema predeterminado. En lugar de empezar una conversación nueva, guiarás la sesión existente para ampliar el trabajo.

## Supervisa el progreso de Copilot

La vista Copilot Chat muestra todos los pasos del agente: los archivos que explora, los comandos que ejecuta y las ediciones que propone. Cada edición aparece en el chat con las diferencias y los controles **Keep** y **Undo**.

1. Vuelve a la vista Copilot Chat desde el ejercicio 4.
2. Mientras trabaja el agente, desplázate por el chat para observar cada llamada a herramienta. Verás lecturas y ediciones de archivos, además de los comandos de terminal que solicite ejecutar.
3. Cuando el agente proponga una edición, las diferencias aparecerán en línea:
   - selecciona **Keep** para aceptar el cambio;
   - selecciona **Undo** para revertirlo y permitir que el agente vuelva a intentarlo.
4. También puedes abrir el indicador **Files changed** de la parte superior del chat para ver todos los archivos modificados durante la sesión.

> [!NOTE]
> Si una llamada a herramienta pide permiso, por ejemplo, para ejecutar un comando de terminal, el agente se detendrá y mostrará una solicitud **Allow**. Aprueba solo los comandos que te parezcan adecuados.

5. Espera a que el agente termine de implementar el modo de alto contraste. Deberías ver un mensaje final que resuma los cambios.

## Guía al agente para añadir el modo claro

Ya está implementado el modo de alto contraste. Ahora ampliarás la misma conversación para añadir un control del modo claro. Como continuarás en la misma sesión de chat, el agente conservará todo el contexto del trabajo anterior, incluidos los componentes modificados, el enfoque de estilos y el patrón de almacenamiento local.

1. En el campo de Copilot Chat, envía esta solicitud de seguimiento:

    ```plaintext
    ¡Buen trabajo! Ahora añade un control del modo claro junto al del modo de alto contraste. El modo claro debe seguir el mismo patrón: conservar la preferencia en el almacenamiento local y estar disponible en la misma zona de la interfaz. Las personas deben poder activar el modo de alto contraste y el modo claro de forma independiente.
    ```

2. El agente continuará desde donde lo dejó, revisará los cambios realizados y propondrá los cambios necesarios para el modo claro.
3. Revisa cada cambio nuevo cuando aparezca, como hiciste con el modo de alto contraste. Selecciona **Keep** o **Undo** para cada diferencia.
4. Si el agente sigue un enfoque que no quieres —por ejemplo, intenta reorganizar de una forma que no te gusta los controles de modo—, detenlo con el botón **Stop** junto al campo de chat y envíale una solicitud aclaratoria.

> [!TIP]
> Guiar una sesión en curso es más rápido que empezar de nuevo. Usa las solicitudes de seguimiento para pedir ajustes («simplifica el CSS: usa variables CSS en lugar de duplicar selectores»), corregir el enfoque («no añadas un tercer control; integra el modo claro en el panel de configuración existente») o ampliar el alcance («añade también métodos abreviados de teclado para ambos controles»).

## Revisa el conjunto de trabajo

Antes de confirmar los cambios, revisa rápidamente todo lo que ha modificado el agente.

1. Abre la vista **Source Control** de VS Code.
2. Revisa la lista completa de archivos modificados. Deberías ver actualizaciones de componentes de Astro, estilos y pruebas relacionadas.
3. Abre algunos archivos modificados y revisa sus diferencias. Confirma que reflejan las prácticas de accesibilidad del agente personalizado: atributos ARIA, navegación con el teclado, HTML semántico y preferencias conservadas en el almacenamiento local.
4. Cuando estés conforme, prepara y confirma los cambios desde el panel Source Control. Los publicarás en [la lección siguiente][next-lesson].

## Resumen y siguientes pasos

Has utilizado el modo de agente de Copilot Chat para supervisar y guiar una sesión en curso: revisaste cada edición de archivo, aceptaste o rechazaste cambios y enviaste solicitudes de seguimiento para ampliar el trabajo sin perder el contexto.

En esta lección has aprendido a:

- supervisar el trabajo de un agente en tiempo real;
- aceptar y rechazar ediciones de archivos concretas;
- guiar una sesión en curso con solicitudes de seguimiento.

En la [lección siguiente][next-lesson] publicarás el trabajo de accesibilidad como solicitud de cambios y revisarás las modificaciones del agente personalizado local que se hayan generado durante el laboratorio.

## Recursos

- [Copilot Chat en VS Code][copilot-chat-vscode]
- [Uso del modo de agente][agent-mode]

---

| [← Lección anterior: Agentes personalizados][previous-lesson] | [Siguiente lección: Itera sobre el trabajo de Copilot →][next-lesson] |
|:--|--:|

[previous-lesson]: ../4-custom-agents/
[next-lesson]: ../6-iterating/
[copilot-chat-vscode]: https://code.visualstudio.com/docs/copilot/chat/copilot-chat
[agent-mode]: https://code.visualstudio.com/docs/copilot/chat/chat-agent-mode
