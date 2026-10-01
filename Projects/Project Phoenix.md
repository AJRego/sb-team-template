---
tags: project
---
Project Phoenix is Driftwood’s next big step: shared family libraries, synced through a server. Led by @jane.

# Milestones
* [x] @jane sync protocol draft
* [ ] @jane sync server prototype
* [ ] @hana mobile app reads a synced library
* [ ] @jo family sharing flow

# Decisions
${query[[
  from p = index.contentPages("adr")
  where p.project == "[[Projects/Project Phoenix]]"
  order by p.date desc
  select {
    ADR = "[[" .. p.name .. "]]",
    Status = p.status,
    Decided = p.date
  }
]]}
