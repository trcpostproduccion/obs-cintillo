# Bitácora del proyecto: Cintillos OBS

## Propósito

Este proyecto ofrece un cintillo (lower-third) para OBS y un panel remoto, accesible desde teléfono o computadora, para administrar conductores e invitados, seleccionar quién sale al aire, controlar el diseño y ajustar posición/tipografía.

## Repositorio y archivos

- Repositorio: `belkinnauta/obs-cintillo`
- `index.html`: vista de overlay transparente que se agrega en OBS como fuente Navegador. Consulta el estado compartido y muestra una sola persona seleccionada.
- `control.html`: panel remoto de producción para gestionar personas, elegir quién sale al aire, ocultar el cintillo y controlar diseño/tipografía/posición.
- `cintillos/`: diseños PNG usados como fondos de los cintillos.
- `BITACORA.md`: este documento; leerlo al retomar el trabajo y confirmar el código vigente.

## Sincronización y estado

El panel y OBS se comunican mediante este endpoint npoint:

`https://api.npoint.io/2b81ca901938404ea4e7`

La vista de OBS consulta el estado cada 2 segundos. El estado mantiene compatibilidad con los campos antiguos y admite una lista de personas:

```json
{
  "titulo": "Persona actualmente al aire",
  "subtitulo": "Cargo actual",
  "personas": [
    { "id": "conductor", "tipo": "conductor", "nombre": "Nombre del conductor", "cargo": "Conductor/a" },
    { "id": "guest-unique-id", "tipo": "invitado", "nombre": "Nombre del invitado", "cargo": "Cargo" }
  ],
  "selectedPersonaId": "conductor",
  "onAirPersonaId": "conductor",
  "bgImage": "https://.../imagen.png",
  "posX": 20,
  "posY": 82,
  "fontSizeNombre": 38,
  "fontSizeCargo": 22,
  "activo": false
}
```

`selectedPersonaId` indica a quién eligió el operador para el próximo envío. `onAirPersonaId` identifica la única persona que el overlay muestra actualmente. Los campos `titulo` y `subtitulo` reflejan esa persona para mantener compatibilidad con versiones anteriores.

- `posX` y `posY` son porcentajes del lienzo OBS.
- Tamaños de tipografía están expresados en píxeles.
- Valores iniciales usados por la página: X=20%, Y=82%, nombre=38px y cargo=22px.
- `activo: false` oculta tanto la imagen como el texto, sin eliminar la lista de personas.

## Flujo de producción acordado

El switcher/director le indica al operador qué persona debe aparecer en la toma. El operador selecciona una sola persona —conductor o invitado— y pulsa «Enviar al aire». OBS muestra únicamente el cintillo de esa persona. «Ocultar cintillo» lo apaga. Las otras personas quedan guardadas, no se borran ni salen simultáneamente.

El panel ofrece:

- una persona fija Conductor/a;
- invitados agregables, editables y removibles;
- radio buttons para seleccionar a una persona a la vez;
- «Guardar lista» para guardar las personas sin cambiar el cintillo actualmente en pantalla;
- «Enviar al aire» para mostrar únicamente la persona seleccionada;
- «Ocultar cintillo» para ocultar el overlay y conservar los datos;
- campo de URL o carga de imagen mediante el bucket público `cintillos` de Supabase;
- controles de tamaño de nombre/cargo;
- botones direccionales para ajustar posición.

## Identidad y diseño de la interfaz

TRC Television y NahuatLabs son identidad/créditos únicamente de la interfaz del usuario. No deben aparecer en los cintillos ni en el overlay de OBS, a menos que el usuario lo solicite expresamente en el futuro.

Nombre institucional presentado en el panel:

- `TRC Television`
- `Sistema de Televisión y Radio de Campeche`

Crédito de desarrollo en el panel:

- `NahuatLabs`

Paleta institucional proporcionada por el usuario:

- Colores claros: `#F1F1F2`, `#9F2241`, `#BAA88D`, `#235B4E`, `#898A8D`
- Colores oscuros: `#393C3F`, `#691C32`, `#AA9678`, `#10312B`, `#54565A`

Dirección visual solicitada: dashboard premium de televisión / control room / broadcast desk, responsive para teléfono, aspecto profesional de operación en vivo. El usuario compartió una imagen del logo de la televisora; la imagen adjunta en conversación se vio en blanco, por lo que verificar si hay un archivo de logo disponible en el repo antes de integrarlo. No inventar ni reconstruir la marca gráfica; por ahora el encabezado puede usar un monograma de texto TRC.

## Imagen y uso en OBS

Diseño predeterminado actual conocido:

`https://raw.githubusercontent.com/belkinnauta/obs-cintillo/main/cintillos/SUPER%20POMUCH%2026.png`

La imagen se ajusta al lienzo mediante `object-fit: contain`; texto sobre una capa transparente. Para uso en OBS se recomienda fuente Navegador en la resolución del lienzo, comúnmente 1920 × 1080. Si la página de OBS queda abierta tras cambios, refrescar la fuente o recargar su URL.

La carga local como data URL puede crear un JSON muy grande y no es una opción fiable entre dispositivos. Preferir imagen con URL pública o subir a Supabase Storage. El panel tiene configurado un cliente Supabase para el proyecto y bucket `cintillos`; la clave publishable está en el HTML y no debe confundirse con una service-role secret. Nunca pedir ni registrar la contraseña de Supabase en esta bitácora.

## Historial de decisiones y cambios

- Se estableció una página lower-third para OBS y panel remoto sincronizados mediante npoint.
- Se adoptó el PNG `SUPER POMUCH 26.png` como arte predeterminado.
- Se decidió que el diseño PNG y el texto desaparezcan juntos al ocultar.
- Se permite ajustar posición X/Y y tamaños de nombre/cargo desde el panel.
- Se resolvió un error JavaScript de declaración duplicada de `supabase` usando un cliente con nombre no conflictivo.
- Se agregó soporte para conductor e invitados persistentes.
- Se aclaró que no deben mostrarse varios nombres a la vez: el operador selecciona exactamente la persona indicada por el switcher.
- Se rediseñó `control.html` como dashboard premium usando la paleta institucional TRC; el nombre de la estación y crédito NahuatLabs se muestran solo en el panel.
- Último commit conocido al actualizar esta bitácora: verificar el SHA real al terminar esta actualización.

## Estado conocido al 2026-11-06

- `control.html` fue actualizado a dashboard premium con paleta TRC, identificación TRC Television, subtítulo institucional, crédito NahuatLabs y flujo de selección individual.
- `index.html` tiene la lógica de mostrar una persona elegida por `onAirPersonaId`; no contiene branding TRC/NahuatLabs en el cintillo.
- El código del repo es la fuente de verdad: antes de modificar funcionalidad, volver a leer ambos HTML porque los cambios recientes pueden no estar reflejados completamente aquí.
- Confirmar el SHA del commit más reciente en GitHub después de guardar esta bitácora.
- Confirmar que GitHub Pages terminó de publicar antes de pedir pruebas del usuario. Los parámetros `?v=N` solo ayudan a invalidar caché del navegador.

## Pruebas pendientes / siguientes pasos

1. Probar en móvil el nuevo dashboard y confirmar que las tarjetas, los radios y los botones se vean bien.
2. Probar el guardado de conductor e invitados y verificar que sobrevivan a recargar el panel.
3. Elegir un invitado, pulsar «Enviar al aire» y verificar que OBS muestre solo ese invitado.
4. Elegir conductor y comprobar el cambio de cintillo sin perder invitados.
5. Pulsar «Ocultar cintillo» y confirmar que desaparece el arte y texto, permaneciendo guardada la lista.
6. Revisar consola/Network si el JSON de npoint no guarda la lista o si el overlay no actualiza.
7. Si se comparte una imagen de logo utilizable, integrar el archivo en la cabecera del panel solamente, no en OBS.

## Cómo retomar el trabajo con Copilot

En una conversación nueva, decir:

> Lee `BITACORA.md` de `belkinnauta/obs-cintillo` para recuperar el contexto del proyecto. Después revisa el estado actual de `control.html` e `index.html`, verifica los commits recientes y continúa desde las tareas pendientes. TRC Television y NahuatLabs son solo branding del panel, no del cintillo.

La bitácora conserva decisiones y contexto, pero no es memoria automática. Al comenzar una nueva conversación, pedir que se lea; después de cambios importantes, actualizar esta bitácora y registrar el commit real.
