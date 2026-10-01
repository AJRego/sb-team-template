All sprints, newest first, collected from pages tagged `#sprint`.

${query[[
  from p = index.contentPages "sprint"
  order by p.name desc
  select templates.pageItem(p)
]]}
