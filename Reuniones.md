Todas las notas de reuniones, de la más reciente a la más antigua, recopiladas de las páginas con etiqueta `#reunión`.

${query[[
  from m = index.contentPages "reunión"
  order by m.fecha desc
  select templates.pageItem(m)
]]}
