#meta

Every decision record opens with a card: its status, when it was decided and by whom, and what it replaced or relates to. Click the card to edit the frontmatter behind it.

```space-lua
local function list(v)
  if type(v) == "string" then
    return { v }
  end
  return v or {}
end

tag.define {
  name = "adr",
  renderFrontmatter = function(adr)
    local status = adr.status or "unknown"
    local meta = dom.div {
      class = "team-adr-meta",
      dom.span { class = "team-chip team-chip-" .. status, status },
    }
    if adr.date then
      meta.appendChild(dom.span { "decided " .. adr.date })
    end
    if adr.owner then
      meta.appendChild(dom.span { "owner " .. adr.owner })
    end
    if adr.project then
      meta.appendChild(dom.span { adr.project })
    end

    local card = dom.div { class = "team team-adr-card", meta }
    local supersededBy = list(adr.supersededBy)
    if #supersededBy > 0 then
      card.appendChild(dom.p { "Superseded by " .. table.concat(supersededBy, ", ") })
    end
    local related = list(adr.related)
    if #related > 0 then
      card.appendChild(dom.p { "Related: " .. table.concat(related, " · ") })
    end
    return widget.htmlBlock(card)
  end,
}
```

```space-style
.team-adr-card {
  padding: 12px 16px;
  border: 1px solid #1f3b3a;
  background: #0b1514;
  color: #ccfbf1;
  font-size: 0.9em;
}
.team-adr-meta {
  display: flex;
  flex-wrap: wrap;
  align-items: center;
  gap: 6px 14px;
  color: #5f8f8a;
}
.team-adr-card p {
  margin: 8px 0 0;
}
```
