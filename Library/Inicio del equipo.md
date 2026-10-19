#meta

Página de inicio del wiki del equipo: el sprint actual, las tareas abiertas de cada persona y el estado de nuestras decisiones, además de las tareas abiertas que se muestran al inicio de cada página de persona. El espacio se abre en modo oscuro.

```space-lua
-- Oscuro por defecto, salvo que hayas elegido un tema con "Editor: Toggle Dark Mode"
if clientStore.get "darkMode" == nil and editor.getUiOption "darkMode" == nil then
  editor.setUiOption("darkMode", true)
end
```

```space-lua
team = {}

-- Los enlaces de los widgets abren páginas con editor.navigate, así funcionan donde sea que se sirva el espacio
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

-- Si una tarea va dirigida a una identidad, escrita como "@hana"
local function addressedTo(task, target)
  return table.find(task.recipients, function(r)
    return r == target
  end) ~= nil
end

local MONTHS = { "enero", "febrero", "marzo", "abril", "mayo", "junio", "julio",
  "agosto", "septiembre", "octubre", "noviembre", "diciembre" }

-- "2026-10-09" como "9 de octubre"
local function longDate(iso)
  local _, m, d = tostring(iso):match "^(%d+)-(%d+)-(%d+)$"
  if not m then
    return tostring(iso)
  end
  return tonumber(d) .. " de " .. MONTHS[tonumber(m)]
end

local function plural(n, sing, plur)
  return n .. " " .. (n == 1 and sing or (plur or sing .. "s"))
end

-- Línea de estado al estilo terminal: tareas abiertas, la última sincronía semanal y las decisiones aún pendientes
function team.header()
  local meetings = query[[
    from p = index.contentPages "reunión"
    order by p.fecha desc
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
    where p.estado == "propuesta"
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
        plural(#open, "tarea abierta", "tareas abiertas"),
      },
      dom.a {
        class = "team-pill",
        onclick = go(meeting),
        "última sincronía " .. meeting:match "%d+%-%d+%-%d+",
      },
      dom.a {
        class = "team-pill team-pill-warn",
        onclick = go "ADR",
        plural(#waiting, "decisión pendiente", "decisiones pendientes"),
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
  local progress = #tasks > 0 and math.max(4, math.floor(100 * done / #tasks)) or 0
  local ends = query[[
    from p = index.pages()
    where p.name == page
    select p.fin
  ]]
  ends = ends[1]
  return widget.htmlBlock(dom.div {
    class = "team team-sprint",
    dom.div {
      class = "team-sprint-head",
      dom.a {
        onclick = go(page),
        page:match "[^/]+$",
      },
      dom.span { done .. " de " .. #tasks .. " hechas" .. (ends and (" · termina " .. longDate(ends)) or "") },
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

-- Tareas abiertas dirigidas a una identidad, escrita como "@hana"
function team.openTasks(id)
  local target = "@" .. id
  return query[[
    from t = index.tasks()
    where not t.done and addressedTo(t, target)
    select templates.taskItem(t)
  ]]
end

-- Encima de toda página con `identity`: las tareas abiertas de esa persona, donde se hayan asignado
view.define {
  name = "team.openTasks",
  title = "Tareas abiertas",
  dock = "page-top",
  frame = "minimal",
  supportedDocks = { "page-top", "page-bottom", "rhs" },
  refreshOn = { "navigate", "index" },
  content = function()
    local id = (editor.getCurrentPageMeta() or {}).identity
    if not id then
      return nil
    end
    local tasks = team.openTasks(id)
    if #tasks == 0 then
      return nil
    end
    return "**Tareas abiertas de @" .. id .. "**\n" .. table.concat(tasks)
  end,
}

-- Tareas abiertas por miembro del equipo: páginas con etiqueta #equipo, emparejadas con las tareas por su `identity` (p. ej. hana, mencionada como @hana)
function team.people()
  local open = query[[
    from t = index.tasks()
    where not t.done and t.recipients
    select t
  ]]
  local members = query[[
    from p = index.contentPages "equipo"
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
    order by p.fecha desc
    select p
  ]]
  local counts, waiting = {}, nil
  for _, a in ipairs(adrs) do
    counts[a.estado] = (counts[a.estado] or 0) + 1
    if a.estado == "propuesta" then
      waiting = a.name
    end
  end
  local chips = {}
  for _, status in ipairs { "aceptada", "propuesta", "reemplazada", "deprecada" } do
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
      "Pendiente de decisión: ",
      dom.a {
        onclick = go(waiting),
        (waiting:gsub("^ADR/", "")),
      },
    } or nil,
  })
end
```

```space-style
/* Color de acento del equipo; el código de arriba abre el espacio en modo oscuro */
html:root {
  --ui-accent-color: #2dd4bf;
}

/* Los widgets de inicio se sitúan en la página sin el marco habitual del widget */
.sb-lua-directive-block:has(.team) {
  border: none !important;
  background: none !important;
}

/* Paneles planos con esquinas rectas, como una terminal */
.team-header,
.team-sprint,
.team-person {
  border: 1px solid #1f3b3a;
  background: #0b1514;
}

/* La línea de estado */
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

/* El sprint actual: barra de progreso y sus tareas */
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

/* Avatares: un color por persona, cuadrados como todo lo demás aquí */
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

/* Tareas abiertas por persona, como barra de carga */
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

/* Dónde están las decisiones, y la que sigue pendiente */
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
.team-chip-aceptada {
  color: #2dd4bf;
}
.team-chip-propuesta {
  color: #fbbf24;
}
.team-chip-reemplazada {
  color: #94a3b8;
}
.team-chip-deprecada {
  color: #f87171;
}
.team-decisions p {
  margin: 10px 0 0;
  font-size: 0.9em;
}
.team-decisions a {
  color: #5eead4 !important;
}

/* Los enlaces de los widgets navegan al pulsarlos, así que muestran el cursor de mano */
.team a {
  cursor: pointer;
}
```
