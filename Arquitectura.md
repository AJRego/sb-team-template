Cómo encaja Driftwood, dibujado en vivo desde el frontmatter de cada página con etiqueta `#componente`. Las decisiones detrás de ello están en [[ADR]].

${mermaid.diagram(mermaid.relationGraph {
  pages = query[[from index.contentPages "componente"]],
  direction = "LR",
})}

# Componentes
${query[[
  from p = index.contentPages "componente"
  order by p.name
  select templates.pageItem(p)
]]}
