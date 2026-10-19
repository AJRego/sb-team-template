---
tags: proyecto
---
Project Phoenix es el siguiente gran paso de Driftwood: bibliotecas familiares compartidas, sincronizadas a través de un servidor. Dirigido por @jane.

# Hitos
* [x] @jane borrador del protocolo de sincronización
* [ ] @jane prototipo de servidor de sincronización
* [ ] @hana la app móvil lee una biblioteca sincronizada
* [ ] @jo flujo de compartir en familia

# Decisiones
${query[[
  from p = index.contentPages "adr"
  where p.proyecto == "[[Proyectos/Project Phoenix]]"
  order by p.fecha desc
  select {
    ADR = "[[" .. p.name .. "]]",
    Estado = p.estado,
    ["Decidido el"] = p.fecha,
  }
]]}
