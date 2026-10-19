Qué estamos construyendo y quién es responsable, recopilado de las páginas con etiqueta `#funcionalidad`.

${query[[
  from f = index.contentPages "funcionalidad"
  order by f.estado
  select {
    Funcionalidad = "[[" .. f.name .. "]]",
    Estado = f.estado,
    Responsable = f.responsable,
  }
]]}
