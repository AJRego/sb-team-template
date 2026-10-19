#meta

Toda página de registro de decisiones abre con una tarjeta: su estado, cuándo se decidió y por quién, y qué reemplaza o con qué se relaciona. Pulsa la tarjeta para editar el frontmatter que hay detrás.

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
    local status = adr.estado or "desconocido"
    local meta = dom.div {
      class = "team-adr-meta",
      dom.span { class = "team-chip team-chip-" .. status, status },
    }
    if adr.fecha then
      meta.appendChild(dom.span { "decidido " .. adr.fecha })
    end
    if adr.responsable then
      meta.appendChild(dom.span { "responsable " .. adr.responsable })
    end
    if adr.proyecto then
      meta.appendChild(dom.span { adr.proyecto })
    end

    local card = dom.div { class = "team team-adr-card", meta }
    local supersededBy = list(adr.supersededBy)
    if #supersededBy > 0 then
      card.appendChild(dom.p { "Reemplazado por " .. table.concat(supersededBy, ", ") })
    end
    local related = list(adr.relacionados)
    if #related > 0 then
      card.appendChild(dom.p { "Relacionados: " .. table.concat(related, " · ") })
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
