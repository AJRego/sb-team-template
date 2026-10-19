---
tags: adr
estado: aceptada
fecha: "2026-08-12"
responsable: "[[Personas/Sam Rivera]]"
---
Guardamos el catálogo en un archivo SQLite por biblioteca, junto a las fotos.

**Contexto.** El catálogo debe funcionar sin conexión, manejar 200k fotos y ser fácil de respaldar.

**Consecuencias.** Los respaldos son "copiar la carpeta". Los escritores concurrentes necesitan un bloqueo.
