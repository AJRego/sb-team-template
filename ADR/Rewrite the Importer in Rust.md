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
@pete before we rewrite it: how fast is the importer since [[Roadmap/Fast Import|Fast Import]] landed? If it’s fast enough, the sync server could just call the one we have. -- @jane

Not measured yet, that’s my profiling task from [[Meetings/Weekly Sync 2026-09-25|last week’s sync]], on the 200k sample library. @jane I’ll post the numbers here, and we decide at Friday’s sync. -- @pete
-->
