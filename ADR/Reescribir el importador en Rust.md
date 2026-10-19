---
tags: adr
estado: propuesta
fecha: "2026-09-21"
responsable: "[[Personas/Pete Johnson]]"
proyecto: "[[Proyectos/Project Phoenix]]"
relacionados:
  - "[[ADR/Sincronizar bibliotecas mediante un servidor]]"
---
Para [[Proyectos/Project Phoenix|Project Phoenix]], reescribir el importador en Rust para que el servidor de sincronización y la app de escritorio compartan una sola implementación.

Pregunta abierta: ¿el salto de velocidad de 10× de [[Roadmap/Importación rápida|Importación rápida]] hace esto innecesario?

<!--
@pete antes de reescribirlo: ¿a qué velocidad va el importador desde que se lanzó [[Roadmap/Importación rápida|Importación rápida]]? Si va lo bastante rápido, el servidor de sincronización podría simplemente llamar al que ya tenemos. -- @jane

Diez veces más rápido en la biblioteca de pruebas pequeña, pero todavía sin medir en la de 200k: esa es mi tarea de perfilado de [[Reuniones/Sincronía semanal 2026-09-25|la última sincronía]]. @jane, publicaré las cifras aquí y decidimos en la sincronía del viernes. -- @pete
-->
