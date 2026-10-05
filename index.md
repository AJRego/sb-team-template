
${team.header()}

${team.sprint "Sprints/Phoenix Sprint 3"}

# Who’s on what
${team.people()}

# Decisions
${team.decisions()}

# How this is built
Plain markdown pages, plus a few SilverBullet features:
* [Tags](https://docs.silverbullet.md/Tag): ADRs, components, meetings, features, sprints and people are tagged pages, and each tag has a page that collects them, like [[ADR]] and [[Architecture]].
* [Tasks](https://docs.silverbullet.md/Task) and [Mentions](https://docs.silverbullet.md/At-Mention): a task in any page that mentions `@hana` shows up under Hana in “Who’s on what” and on her page.
* [Identities](https://docs.silverbullet.md/Identity): everyone on the team has a person page they can be mentioned by.
* [Mermaid](https://github.com/silverbulletmd/silverbullet-diagram): the diagrams in [[Architecture]] are text you can edit.
* [Space Lua](https://docs.silverbullet.md/Space%20Lua) and [Space Style](https://docs.silverbullet.md/Space%20Style): the header, sprint, people and decision widgets live in [[Library/Team Home]] and [[Library/Decision Card]].
