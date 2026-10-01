---
tags: adr
status: proposed
date: "2026-09-20"
owner: "[[People/Pete Johnson]]"
project: "[[Projects/Project Phoenix]]"
related:
  - "[[ADR/Sync Libraries Through a Server]]"
---
For [[Projects/Project Phoenix|Project Phoenix]], rewrite the importer in Rust so the sync server and the desktop app share one implementation.

Open question: does the 10× speed-up from [[Roadmap/Fast Import|Fast Import]] make this unnecessary?

<!--
@pete have we measured the import since [[Roadmap/Fast Import|Fast Import]] landed? If it’s fast enough now, I’d rather not maintain a second importer. -- @hana

Fair point. @mateo, could you time an import of the 50,000-photo test library this week? Then we decide at Monday’s sync. -- @pete
-->
