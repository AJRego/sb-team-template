Registros de decisiones de arquitectura: por qué Driftwood está construido como está, incluidas las decisiones de [[Proyectos/Project Phoenix|Project Phoenix]].

${query[[
  from p = index.contentPages "adr"
  group by p.estado
  order by key
  select {
    Estado = key,
    Total = #group,
  }
]]}

# Vigentes
${query[[
  from p = index.contentPages "adr"
  where p.estado ~= "reemplazada" and p.estado ~= "deprecada"
  order by p.fecha desc
  select {
    ADR = "[[" .. p.name .. "]]",
    Estado = p.estado,
    Responsable = p.responsable,
    ["Decidido el"] = p.fecha,
  }
]]}

# Reemplazadas y deprecadas
${query[[
  from p = index.contentPages "adr"
  where p.estado == "reemplazada" or p.estado == "deprecada"
  order by p.fecha desc
  select {
    ADR = "[[" .. p.name .. "]]",
    Estado = p.estado,
    ["Reemplazado por"] = p.supersededBy,
    ["Decidido el"] = p.fecha,
  }
]]}

# Conexiones
Los bordes punteados marcan reemplazos. Pulsa un nodo para abrir el ADR.

${mermaid.diagram(mermaid.relationGraph {
  pages = query[[from index.contentPages "adr"]],
  direction = "TD",
})}
