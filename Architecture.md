How Driftwood fits together, drawn live from the frontmatter of every page tagged `#component`. The decisions behind it are in [[ADR]].

${mermaid.diagram(mermaid.relationGraph {
  pages = query[[from index.contentPages "component"]],
  direction = "LR",
})}

# Components
${query[[
  from p = index.contentPages "component"
  order by p.name
  select templates.pageItem(p)
]]}
