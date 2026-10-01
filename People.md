Who works on Driftwood, and who we work with.

# Team
Collected from pages tagged `#team`.

${query[[
  from p = index.contentPages "team"
  order by p.name
  select templates.pageItem(p)
]]}

# Stakeholders
People tagged `#person` who aren’t on the team.

${query[[
  from p = index.contentPages "person"
  where not table.includes(p.tags, "team")
  order by p.name
  select templates.pageItem(p)
]]}
