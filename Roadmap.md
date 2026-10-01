What we’re building and who owns it, collected from pages tagged `#feature`.

${query[[
  from f = index.contentPages "feature"
  order by f.status
  select {
    Feature = "[[" .. f.name .. "]]",
    Status = f.status,
    Owner = f.owner,
  }
]]}
