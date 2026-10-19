---
tags: adr
estado: aceptada
fecha: "2026-07-14"
responsable: "[[Personas/Jane Holland]]"
proyecto: "[[Proyectos/Project Phoenix]]"
relacionados:
  - "[[ADR/Usar SQLite para el catálogo]]"
---
Para [[Proyectos/Project Phoenix|Project Phoenix]], cada dispositivo sincroniza su biblioteca a través de un pequeño servidor de sincronización autoalojable.

**Contexto.** La sincronización entre pares se estancó (véase [[ADR/Sincronizar bibliotecas entre pares]]): los móviles y los portátiles rara vez están en línea al mismo tiempo. [[Personas/Jane Holland|Jane Holland]] propuso un servidor que retiene los cambios de cada biblioteca hasta que todos los dispositivos los tienen.

**Consecuencias.** Las familias necesitan una máquina siempre encendida, o un servidor de sincronización alojado.
