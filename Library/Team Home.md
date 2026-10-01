#meta

The team wiki’s home page: the current sprint, open tasks per person and where our decisions stand, plus `team.openTasks` for person pages. The space opens in dark mode.

```space-lua
-- Dark by default, unless you picked a theme yourself with "Editor: Toggle Dark Mode"
if clientStore.get "darkMode" == nil and editor.getUiOption "darkMode" == nil then
  editor.setUiOption("darkMode", true)
end
```

```space-lua
team = {}

-- Widget links open pages through editor.navigate, so they work wherever the space is served
local function go(ref)
  return function()
    editor.navigate(ref)
  end
end

local function avatar(handle)
  return dom.span {
    class = "team-avatar team-" .. handle,
    handle:sub(1, 1):upper(),
  }
end

-- Whether a task is addressed to an identity, written as "@hana"
local function addressedTo(task, target)
  return table.find(task.recipients, function(r)
    return r == target
  end) ~= nil
end

local function plural(n, word)
  return n .. " " .. word .. (n == 1 and "" or "s")
end

-- A terminal-style status line: open tasks, the last weekly sync and decisions still waiting
function team.header()
  local meetings = query[[
    from p = index.contentPages "meeting"
    order by p.date desc
    limit 1
    select p.name
  ]]
  local meeting = meetings[1]
  local open = query[[
    from t = index.tasks()
    where not t.done and t.recipients
    select t
  ]]
  local waiting = query[[
    from p = index.contentPages "adr"
    where p.status == "proposed"
    select p
  ]]
  return widget.htmlBlock(dom.div {
    class = "team team-header",
    dom.div {
      class = "team-prompt",
      dom.span { class = "team-host", "driftwood" },
      dom.span { ":~$ status" },
      dom.span { class = "team-cursor", "_" },
    },
    dom.div {
      class = "team-pills",
      dom.span {
        class = "team-pill",
        plural(#open, "open task"),
      },
      dom.a {
        class = "team-pill",
        onclick = go(meeting),
        "last sync " .. meeting:match "%d+%-%d+%-%d+",
      },
      dom.a {
        class = "team-pill team-pill-warn",
        onclick = go "ADR",
        plural(#waiting, "decision") .. " waiting",
      },
    },
  })
end

function team.sprint(page)
  local tasks = query[[
    from t = index.tasks()
    where t.page == page
    select t
  ]]
  local done = 0
  local rows = {}
  for _, t in ipairs(tasks) do
    if t.done then
      done = done + 1
    end
    table.insert(rows, dom.a {
      class = t.done and "team-task team-done" or "team-task",
      onclick = go(t.ref),
      avatar(t.text:match "@(%w+)"),
      dom.span { (t.text:gsub("@%w+ ", "")) },
    })
  end
  local progress = math.max(4, math.floor(100 * done / #tasks))
  return widget.htmlBlock(dom.div {
    class = "team team-sprint",
    dom.div {
      class = "team-sprint-head",
      dom.a {
        onclick = go(page),
        page:match "[^/]+$",
      },
      dom.span { done .. " of " .. #tasks .. " done · ends 10 October" },
    },
    dom.div {
      class = "team-bar",
      dom.div { style = "width:" .. progress .. "%" },
    },
    dom.div {
      class = "team-tasks",
      table.unpack(rows),
    },
  })
end

-- Open tasks addressed to an identity; on a person page, ${team.openTasks()} uses the page's own `identity`
function team.openTasks(id)
  if not id then
    local name = editor.getCurrentPage()
    local identities = query[[
      from p = index.pages()
      where p.name == name
      select p.identity
    ]]
    id = identities[1]
  end
  local target = "@" .. id
  return query[[
    from t = index.tasks()
    where not t.done and addressedTo(t, target)
    select templates.taskItem(t)
  ]]
end

-- Open tasks per team member: pages tagged #team, matched to tasks by their `identity` (e.g. hana, addressed as @hana)
function team.people()
  local open = query[[
    from t = index.tasks()
    where not t.done and t.recipients
    select t
  ]]
  local members = query[[
    from p = index.contentPages "team"
    order by p.name
    select p
  ]]
  local cards = {}
  for _, p in ipairs(members) do
    local n = 0
    for _, t in ipairs(open) do
      if addressedTo(t, "@" .. p.identity) then
        n = n + 1
      end
    end
    table.insert(cards, dom.a {
      class = "team-person",
      onclick = go(p.name),
      avatar(p.identity),
      dom.span { p.name:match "[^/]+$" },
      dom.span { class = "team-load", string.rep("■", n) },
      dom.strong { tostring(n) },
    })
  end
  return widget.htmlBlock(dom.div {
    class = "team team-people",
    table.unpack(cards),
  })
end

function team.decisions()
  local adrs = query[[
    from p = index.contentPages "adr"
    order by p.date desc
    select p
  ]]
  local counts, waiting = {}, nil
  for _, a in ipairs(adrs) do
    counts[a.status] = (counts[a.status] or 0) + 1
    if a.status == "proposed" then
      waiting = a.name
    end
  end
  local chips = {}
  for _, status in ipairs { "accepted", "proposed", "superseded", "deprecated" } do
    table.insert(chips, dom.span {
      class = "team-chip team-chip-" .. status,
      (counts[status] or 0) .. " " .. status,
    })
  end
  return widget.htmlBlock(dom.div {
    class = "team team-decisions",
    dom.div {
      class = "team-chips",
      table.unpack(chips),
    },
    waiting and dom.p {
      "Waiting on a decision: ",
      dom.a {
        onclick = go(waiting),
        (waiting:gsub("^ADR/", "")),
      },
    } or nil,
  })
end
```

```space-style
/* The team’s accent colour; the code above opens the space in dark mode */
html:root {
  --ui-accent-color: #2dd4bf;
}

/* Let the home widgets sit on the page without the usual widget frame */
.sb-lua-directive-block:has(.team) {
  border: none !important;
  background: none !important;
}

/* Flat panels with square corners, like a terminal */
.team-header,
.team-sprint,
.team-person {
  border: 1px solid #1f3b3a;
  background: #0b1514;
}

/* The status line */
.team-header {
  padding: 14px 16px;
  color: #ccfbf1;
}
.team-prompt {
  font-size: 1.3em;
  font-weight: 700;
}
.team-host {
  color: #2dd4bf;
}
.team-cursor {
  margin-left: 4px;
  color: #2dd4bf;
  animation: team-blink 1s steps(1) infinite;
}
@keyframes team-blink {
  50% { opacity: 0; }
}
.team-pills {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
  margin-top: 10px;
}
.team-pill {
  padding: 2px 10px;
  border: 1px solid #2dd4bf;
  color: #5eead4 !important;
  font-size: 0.8em;
  text-decoration: none !important;
}
.team-pill::before {
  content: "● ";
}
.team-pill-warn {
  border-color: #fbbf24;
  color: #fbbf24 !important;
}

/* The current sprint: progress bar and its tasks */
.team-sprint {
  padding: 12px 16px;
}
.team-sprint-head {
  display: flex;
  justify-content: space-between;
  font-size: 0.85em;
  text-transform: uppercase;
  letter-spacing: 0.05em;
}
.team-sprint-head a {
  font-weight: 700;
  color: #5eead4 !important;
}
.team-sprint-head span {
  color: #5f8f8a;
}
.team-bar {
  height: 6px;
  margin: 8px 0 10px;
  background: #1f3b3a;
}
.team-bar div {
  height: 100%;
  background: #2dd4bf;
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
  color: #ccfbf1 !important;
  text-decoration: none !important;
}
.team-task:hover span {
  color: #5eead4;
  text-decoration: underline;
}
.team-task::before {
  content: "[ ]";
  flex: none;
  color: #5f8f8a;
}
.team-done::before {
  content: "[x]";
}

/* Avatars: one colour per person, square like everything else here */
.team-avatar {
  display: inline-flex;
  align-items: center;
  justify-content: center;
  flex: none;
  width: 22px;
  height: 22px;
  color: #0b1514;
  font-size: 0.75em;
  font-weight: 700;
}
.team-sam {
  background: #2dd4bf;
}
.team-pete {
  background: #60a5fa;
}
.team-jo {
  background: #f472b6;
}
.team-jane {
  background: #a78bfa;
}
.team-mateo {
  background: #fb923c;
}
.team-hana {
  background: #a3e635;
}

/* Open tasks per person, as a load bar */
.team-people {
  display: grid;
  grid-template-columns: repeat(2, 1fr);
  gap: 6px;
}
.team-person {
  display: flex;
  align-items: center;
  gap: 8px;
  padding: 6px 10px;
  color: #ccfbf1 !important;
  text-decoration: none !important;
  font-size: 0.9em;
}
.team-person span {
  white-space: nowrap;
}
.team-load {
  margin-left: auto;
  color: #2dd4bf;
  letter-spacing: 2px;
}
.team-person strong {
  width: 1.5em;
  text-align: right;
  color: #5eead4;
}

/* Where decisions stand, and the one still waiting */
.team-chips {
  display: flex;
  flex-wrap: wrap;
  gap: 8px;
}
.team-chip {
  padding: 2px 10px;
  border: 1px solid currentColor;
  font-size: 0.85em;
}
.team-chip-accepted {
  color: #2dd4bf;
}
.team-chip-proposed {
  color: #fbbf24;
}
.team-chip-superseded {
  color: #94a3b8;
}
.team-chip-deprecated {
  color: #f87171;
}
.team-decisions p {
  margin: 10px 0 0;
  font-size: 0.9em;
}
.team-decisions a {
  color: #5eead4 !important;
}

/* Widget links navigate on click, so give them a pointer */
.team a {
  cursor: pointer;
}
```
