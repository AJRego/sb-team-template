
${team.header()}

${team.sprint "Sprints/Phoenix Sprint 3"}

# Quién hace qué
${team.people()}

# Decisiones
${team.decisions()}

# Cómo está construido
Páginas markdown corrientes, más algunas funciones de SilverBullet:
* [Tags](https://docs.silverbullet.md/Tag): los ADR, componentes, reuniones, funcionalidades, sprints y personas son páginas etiquetadas, y cada etiqueta tiene una página que las recoge, como [[ADR]] y [[Arquitectura]].
* [Tasks](https://docs.silverbullet.md/Task) y [Mentions](https://docs.silverbullet.md/At-Mention): una tarea en cualquier página que mencione a `@hana` aparece bajo Hana en "Quién hace qué" y en su propia página.
* [Identities](https://docs.silverbullet.md/Identity): cada miembro del equipo tiene una página de persona por la que se le puede mencionar.
* [Mermaid](https://github.com/silverbulletmd/silverbullet-diagram): los diagramas de [[Arquitectura]] son texto que puedes editar.
* [Space Lua](https://docs.silverbullet.md/Space%20Lua) y [Space Style](https://docs.silverbullet.md/Space%20Style): los widgets de cabecera, sprint, personas y decisiones viven en [[Library/Inicio del equipo]] y [[Library/Tarjeta de decisión]].
