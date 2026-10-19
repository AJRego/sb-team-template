Quién trabaja en Driftwood y con quién trabajamos.

# Equipo
Recopilado de las páginas con etiqueta `#equipo`.

${query[[
  from p = index.contentPages "equipo"
  order by p.name
  select templates.pageItem(p)
]]}

# Partes interesadas
Personas con etiqueta `#persona` que no están en el equipo.

${query[[
  from p = index.contentPages "persona"
  where not table.includes(p.tags, "equipo")
  order by p.name
  select templates.pageItem(p)
]]}
