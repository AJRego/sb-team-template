#meta

Space configuration: the space tree docked on the left, and clicking a tag opens the page that collects it.

```space-lua
config.set("view.defaults", {
  ["std.spaceTree"] = { dock = "lhs", open = true },
})

tag.define { name = "adr", tagPage = "ADR" }
tag.define { name = "component", tagPage = "Architecture" }
tag.define { name = "meeting", tagPage = "Meetings" }
tag.define { name = "feature", tagPage = "Roadmap" }
tag.define { name = "person", tagPage = "People" }
tag.define { name = "team", tagPage = "People" }
tag.define { name = "sprint", tagPage = "Sprints" }
tag.define { name = "project", tagPage = "Projects" }
```
