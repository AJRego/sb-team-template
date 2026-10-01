---
tags: adr
status: accepted
date: "2026-07-14"
owner: "[[People/Jane Holland]]"
project: "[[Projects/Project Phoenix]]"
related:
  - "[[ADR/Use SQLite for the Catalog]]"
---
For [[Projects/Project Phoenix|Project Phoenix]], every device syncs its library through a small self-hostable sync server.

**Context.** Syncing peer to peer stalled (see [[ADR/Sync Libraries Peer to Peer]]): phones and laptops are rarely online at the same time. [[People/Jane Holland|Jane Holland]] proposed a server that holds each library’s changes until every device has them.

**Consequences.** Families need one always-on machine, or a hosted sync server.
