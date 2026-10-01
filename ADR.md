Architecture Decision Records: why Driftwood is built the way it is, including the decisions for [[Projects/Project Phoenix|Project Phoenix]].

${query[[
  from p = index.contentPages "adr"
  group by p.status
  order by key
  select {
    Status = key,
    Count = #group,
  }
]]}

# Active
${query[[
  from p = index.contentPages "adr"
  where p.status ~= "superseded" and p.status ~= "deprecated"
  order by p.date desc
  select {
    ADR = "[[" .. p.name .. "]]",
    Status = p.status,
    Owner = p.owner,
    Decided = p.date,
  }
]]}

# Superseded and deprecated
${query[[
  from p = index.contentPages "adr"
  where p.status == "superseded" or p.status == "deprecated"
  order by p.date desc
  select {
    ADR = "[[" .. p.name .. "]]",
    Status = p.status,
    ["Superseded by"] = p.supersededBy,
    Decided = p.date,
  }
]]}

# Connections
Dotted edges mark supersession. Click a node to open the ADR.

${mermaid.diagram(mermaid.relationGraph {
  pages = query[[from index.contentPages "adr"]],
  direction = "TD",
})}
