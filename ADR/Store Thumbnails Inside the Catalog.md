---
tags: adr
status: deprecated
date: "2026-08-20"
owner: "[[People/Pete Johnson]]"
related:
  - "[[ADR/Use SQLite for the Catalog]]"
---
We tried keeping thumbnails as blobs inside the catalog; it made the catalog file too large to back up casually, so the [[Architecture/Thumbnail Cache|Thumbnail Cache]] replaced it.
