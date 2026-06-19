# LayoutView

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

`ui.LayoutView` is the root of every CV2 message. Think of it as the empty canvas you stack `Container`s onto. You never send loose components directly — they always live inside a `LayoutView`.

## Basic Shape

```python
import discord
from discord import ui

class WelcomeLayout(ui.LayoutView):
    def __init__(self):
        super().__init__(timeout=None)
        container = ui.Container(accent_color=discord.Colour.from_rgb(88, 101, 242))
        container.add_item(ui.TextDisplay("# Welcome to LuxorX"))
        self.add_item(container)

await interaction.response.send_message(view=WelcomeLayout())
```

## Subclassing for State

Most real bots subclass `LayoutView` so the layout can hold data and rebuild itself (useful for pagination or live-updating panels):

```python
class QueueLayout(ui.LayoutView):
    def __init__(self, queue: list[str]):
        super().__init__(timeout=300)
        self.queue = queue
        self._build()

    def _build(self):
        self.clear_items()
        container = ui.Container()
        container.add_item(ui.TextDisplay(f"## Music Queue ({len(self.queue)} tracks)"))
        for i, track in enumerate(self.queue[:5], start=1):
            container.add_item(ui.TextDisplay(f"{i}. {track}"))
        self.add_item(container)
```

## Sending vs. Editing

```python
# First send
msg_view = QueueLayout(queue=current_queue)
await interaction.response.send_message(view=msg_view)

# Later edit (e.g. after a track finishes)
msg_view.queue = updated_queue
msg_view._build()
await interaction.edit_original_response(view=msg_view)
```

## Notes

- `timeout` works the same as classic `ui.View` — `None` means it never expires on its own.
- A `LayoutView` can hold multiple `Container`s if you need visually distinct blocks in one message.
- You cannot mix `content=` or `embeds=` into the same `send_message` call as a `view=LayoutView()`.

---

Next: [Container →](Container.md)
