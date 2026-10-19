Hacia qué trabajamos, recopilado de las páginas con etiqueta `#proyecto`.

${query[[
  from p = index.contentPages "proyecto"
  order by p.name
  select templates.pageItem(p)
]]}
