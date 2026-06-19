# Container

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

`ui.Container` is the visual "box" that everything else sits inside. It supports an optional `accent_color`, which renders as a thin colored bar on the left edge — the spiritual successor to `embed.color`.

## Basic Usage

```python
container = ui.Container(accent_color=discord.Colour.gold())
container.add_item(ui.TextDisplay("# Server Boost Goal"))
container.add_item(ui.TextDisplay("14 / 20 boosts reached"))
```

## Multiple Containers in One Message

You can stack more than one `Container` inside a single `LayoutView` to create visually separated sections — handy for a "summary + details" style message.

```python
class ReportLayout(ui.LayoutView):
    def __init__(self):
        super().__init__(timeout=None)

        summary = ui.Container(accent_color=discord.Colour.blue())
        summary.add_item(ui.TextDisplay("## Daily Report — krishna.xyz bot"))
        summary.add_item(ui.TextDisplay("**New members:** 32\n**Messages:** 4,108"))

        warnings = ui.Container(accent_color=discord.Colour.orange())
        warnings.add_item(ui.TextDisplay("⚠️ **3 automod flags** require review"))

        self.add_item(summary)
        self.add_item(warnings)
```

## Spoiler Containers

Containers also support a `spoiler` flag, which blurs the whole block until clicked — useful for spoiler-tagged announcement content:

```python
container = ui.Container(spoiler=True)
container.add_item(ui.TextDisplay("Patch notes leak below 👀"))
```

## Notes

- `accent_color=None` (the default) renders with no colored bar — a flat, neutral block.
- A `Container` with no `accent_color` set is the closest visual match to a plain embed with no `color` field.
- There's a hard cap of **40 total components** per message, and everything inside a `Container` counts toward that limit.

---

Previous: [← LayoutView](Layout-View.md) · Next: [Text Display →](Text-Display.md)
