#meta

The team wiki’s home page: the current sprint, open tasks per person and where our decisions stand, plus `team.openTasks` for person pages.

```space-lua
team = {}

local function avatar(handle)
  return dom.span { class = "team-avatar team-" .. handle, handle:sub(1, 1):upper() }
end

function team.header()
  local meetings = query[[from p = index.contentPages "meeting" order by p.date desc limit 1 select p.name]]
  local meeting = meetings[1]
  return widget.htmlBlock(dom.div {
    class = "team team-header",
    dom.div { dom.h1 { "Driftwood" }, dom.p { "The team wiki: how we work, what we decided, what we’re building." } },
    dom.a { class = "team-meeting", href = "/" .. meeting, dom.span { "Last sync" }, dom.strong { meeting:match "%d+%-%d+%-%d+" } },
  })
end

function team.sprint(page)
  local tasks = query[[from t = index.tasks() where t.page == page select t]]
  local done = 0
  local rows = {}
  for _, t in ipairs(tasks) do
    if t.done then done = done + 1 end
    local handle = t.text:match "@(%w+)"
    table.insert(rows, dom.div { class = t.done and "team-task team-done" or "team-task", avatar(handle), dom.span { (t.text:gsub("@%w+ ", "")) } })
  end
  return widget.htmlBlock(dom.div {
    class = "team team-sprint",
    dom.div { class = "team-sprint-head", dom.a { href = "/" .. page, page:match "[^/]+$" }, dom.span { done .. " of " .. #tasks .. " done · ends 10 October" } },
    dom.div { class = "team-bar", dom.div { style = "width:" .. math.max(4, math.floor(100 * done / #tasks)) .. "%" } },
    dom.div { class = "team-tasks", table.unpack(rows) },
  })
end

-- Open tasks addressed to an identity; on a person page, ${team.openTasks()} uses the page's own `identity`
function team.openTasks(id)
  if not id then
    local name = editor.getCurrentPage()
    local identities = query[[from p = index.pages() where p.name == name select p.identity]]
    id = identities[1]
  end
  local target = "@" .. id
  return query[[
    from t = index.tasks()
    where not t.done and table.find(t.recipients, function(r) return r == target end)
    select templates.taskItem(t)
  ]]
end

-- Open tasks per team member: pages tagged #team, matched to tasks by their `identity` (e.g. hana, addressed as @hana)
function team.people()
  local open = query[[from t = index.tasks() where not t.done and t.recipients select t]]
  local cards = {}
  for _, p in ipairs(query[[from p = index.contentPages "team" order by p.name select p]]) do
    local n = 0
    for _, t in ipairs(open) do
      if table.find(t.recipients, function(r) return r == "@" .. p.identity end) then n = n + 1 end
    end
    table.insert(cards, dom.a {
      class = "team-person",
      href = "/" .. p.name,
      avatar(p.identity), dom.span { p.name:match "[^/]+$" }, dom.strong { tostring(n) },
    })
  end
  return widget.htmlBlock(dom.div { class = "team team-people", table.unpack(cards) })
end

function team.decisions()
  local adrs = query[[from p = index.contentPages "adr" order by p.date desc select p]]
  local counts, waiting = {}, nil
  for _, a in ipairs(adrs) do
    counts[a.status] = (counts[a.status] or 0) + 1
    if a.status == "proposed" then waiting = a.name end
  end
  local chips = {}
  for _, s in ipairs { "accepted", "proposed", "superseded", "deprecated" } do
    table.insert(chips, dom.span { class = "team-chip team-chip-" .. s, (counts[s] or 0) .. " " .. s })
  end
  return widget.htmlBlock(dom.div {
    class = "team team-decisions",
    dom.div { class = "team-chips", table.unpack(chips) },
    waiting and dom.p { "Waiting on a decision: ", dom.a { href = "/" .. waiting, (waiting:gsub("^ADR/", "")) } } or nil,
  })
end
```

```space-style
/* The team’s accent colour */
html:root {
  --ui-accent-color: #0d9488;
}

/* Let the home widgets sit on the page without the usual widget frame */
.sb-lua-directive-block:has(.team) {
  border: none !important;
  background: none !important;
}

/* Banner with the link to the last weekly sync */
.team-header {
  display: flex;
  justify-content: space-between;
  align-items: center;
  gap: 16px;
  padding: 18px 20px;
  border-radius: 14px;
  background: linear-gradient(135deg, #0f766e, #14b8a6);
  color: #fff;
}
.team-header h1 {
  margin: 0;
  font-size: 1.9em;
  color: #fff;
}
.team-header p {
  margin: 4px 0 0;
  font-size: 0.85em;
  color: #ccfbf1;
}
.team-meeting {
  display: flex;
  flex-direction: column;
  align-items: flex-end;
  padding: 8px 12px;
  border-radius: 10px;
  background: rgba(255,255,255,.16);
  color: #fff !important;
  text-decoration: none !important;
  white-space: nowrap;
}
.team-meeting span {
  font-size: 0.75em;
  color: #ccfbf1;
}

/* The current sprint: progress bar and its tasks */
.team-sprint {
  padding: 14px 16px;
  border: 1px solid #99f6e4;
  border-radius: 12px;
  background: #f0fdfa;
}
.team-sprint-head {
  display: flex;
  justify-content: space-between;
  font-size: 0.9em;
}
.team-sprint-head a {
  font-weight: 700;
  color: #115e59 !important;
}
.team-sprint-head span {
  color: #0f766e;
}
.team-bar {
  height: 8px;
  margin: 8px 0 10px;
  border-radius: 99px;
  background: #ccfbf1;
}
.team-bar div {
  height: 100%;
  border-radius: 99px;
  background: #14b8a6;
}
.team-tasks {
  display: flex;
  flex-direction: column;
  gap: 6px;
}
.team-task {
  display: flex;
  align-items: center;
  gap: 10px;
  font-size: 0.9em;
}

/* Avatars: one colour per person */
.team-avatar {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex: none;
  width: 26px;
  height: 26px;
  border-radius: 50%;
  color: #fff;
  font-size: 0.8em;
  font-weight: 700;
}
.team-sam {
  background: #0d9488;
}
.team-pete {
  background: #2563eb;
}
.team-jo {
  background: #db2777;
}
.team-jane {
  background: #7c3aed;
}
.team-mateo {
  background: #ea580c;
}
.team-hana {
  background: #65a30d;
}

/* Open tasks per person */
.team-people {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 8px;
}
.team-person {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 8px 10px;
  border: 1px solid #ccfbf1;
  border-radius: 10px;
  color: #134e4a !important;
  text-decoration: none !important;
  font-size: 0.9em;
}
.team-person strong {
  margin-left: auto;
  color: #0d9488;
}

/* Where decisions stand, and the one still waiting */
.team-chips {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}
.team-chip {
  padding: 4px 12px;
  border-radius: 99px;
  font-size: 0.85em;
  font-weight: 600;
}
.team-chip-accepted {
  background: #ccfbf1;
  color: #115e59;
}
.team-chip-proposed {
  background: #fef3c7;
  color: #92400e;
}
.team-chip-superseded {
  background: #e7e5e4;
  color: #57534e;
}
.team-chip-deprecated {
  background: #fee2e2;
  color: #991b1b;
}
.team-decisions p {
  margin: 10px 0 0;
  font-size: 0.9em;
}
```
