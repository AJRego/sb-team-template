#meta

Configuración del espacio: el árbol del espacio anclado a la izquierda, y al pulsar una etiqueta se abre la página que la recoge.

```space-lua
config.set("view.defaults", {
  ["std.spaceTree"] = {
    dock = "lhs",
    open = true,
  },
})

tag.define {
  name = "adr",
  tagPage = "ADR",
}

tag.define {
  name = "componente",
  tagPage = "Arquitectura",
}

tag.define {
  name = "reunión",
  tagPage = "Reuniones",
}

tag.define {
  name = "funcionalidad",
  tagPage = "Roadmap",
}

tag.define {
  name = "persona",
  tagPage = "Personas",
}

tag.define {
  name = "equipo",
  tagPage = "Personas",
}

tag.define {
  name = "sprint",
  tagPage = "Sprints",
}

tag.define {
  name = "proyecto",
  tagPage = "Proyectos",
}
```
