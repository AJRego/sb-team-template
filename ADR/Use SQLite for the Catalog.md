---
tags: adr
status: accepted
date: "2026-08-12"
owner: "[[People/Sam Rivera]]"
---
We store the catalog in one SQLite file per library, next to the photos.

**Context.** The catalog must work offline, handle 200k photos and be easy to back up.

**Consequences.** Backups are “copy the folder”. Concurrent writers need a lock.
