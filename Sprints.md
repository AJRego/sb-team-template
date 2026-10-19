Todos los sprints, del más reciente al más antiguo, recopilado de las páginas con etiqueta `#sprint`.

${query[[
  from p = index.contentPages "sprint"
  order by p.name desc
  select templates.pageItem(p)
]]}
