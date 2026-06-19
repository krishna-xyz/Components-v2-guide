# Tips & Gotchas

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

A grab-bag of things that'll trip you up the first time you build with CV2, collected from real LuxorX bot deployments.

## Installation

CV2 isn't in the PyPI release yet — install straight from GitHub:

```bash
pip install git+https://github.com/Rapptz/discord.py
```

If you're already on an older `discord.py` version, uninstall first to avoid a mixed environment:

```bash
pip uninstall discord.py -y
pip install git+https://github.com/Rapptz/discord.py
```

## The 40-Component Limit

Every `TextDisplay`, `Separator`, `Button`, `Section`, etc. counts toward a single shared cap of **40 components per message**, including nested ones inside `Container`s. A layout that looks simple can blow past this fast if you're generating rows in a loop — always count, don't assume.

```python
# Bad: no cap, could exceed 40 if items is long
for item in items:
    container.add_item(ui.TextDisplay(item))

# Better: cap explicitly and note overflow
MAX_ITEMS = 30
for item in items[:MAX_ITEMS]:
    container.add_item(ui.TextDisplay(item))
if len(items) > MAX_ITEMS:
    container.add_item(ui.TextDisplay(f"...and {len(items) - MAX_ITEMS} more"))
```

## CV2 vs. Classic Send Args

You cannot combine these in the same `send`/`send_message` call as a CV2 `view=`:

- `content=`
- `embeds=`
- `stickers=`
- `poll=`

Files are fine alongside CV2 — `MediaGallery` and `File` components specifically depend on attachments being passed via `file=`/`files=`.

## Mobile Rendering Differs From Desktop

`Section` accessories and multi-column-feeling layouts can wrap differently on mobile. Always test with a phone or the mobile preview before shipping a layout to a large server — what looks tight on desktop can look cramped or oddly stacked on a small screen.

## Custom ID Collisions

If two different `Container`s in the same `LayoutView` reuse a `custom_id`, only one callback will ever fire reliably. Prefix IDs by feature to stay safe:

```python
custom_id=f"luxorx_ticket_close_{ticket_id}"
custom_id=f"luxorx_warn_revoke_{case_id}"
```

## Don't Forget `timeout`

`LayoutView(timeout=None)` is common for persistent panels (ticket buttons, role pickers) that need to work after a bot restart — but those require the view to also be registered with `bot.add_view()` on startup, same as classic persistent views:

```python
@bot.event
async def on_ready():
    bot.add_view(TicketOpenLayout())
```

## Editing Is Manual

There's no automatic re-render — if your layout depends on changing data, rebuild it explicitly and call `edit_message`/`edit_original_response` with the fresh view. CV2 doesn't diff anything for you.

---

Previous: [← Real-World Examples](Real-World-Examples.md) · [Back to main guide](README.md)
