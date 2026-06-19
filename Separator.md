# Separator

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

`ui.Separator` adds either a visible divider line or pure vertical spacing between components — useful for breaking a wall of text into readable sections.

## Basic Usage

```python
container.add_item(ui.TextDisplay("# Patch Notes — v2.4.0"))
container.add_item(ui.Separator())
container.add_item(ui.TextDisplay("**Fixed:** Ticket buttons not closing on mobile"))
```

## Spacing-Only Mode

Pass `divider=False` if you want breathing room between blocks without an actual visible line:

```python
container.add_item(ui.TextDisplay("## LuxorX Dev Log"))
container.add_item(ui.Separator(divider=False, spacing=discord.SeparatorSpacing.large))
container.add_item(ui.TextDisplay("Entry written by krishna.xyz — discord.gg/kaleshi"))
```

## Real Example — Sectioned Changelog

```python
def build_changelog(entries: dict[str, list[str]]) -> ui.Container:
    container = ui.Container(accent_color=discord.Colour.purple())
    container.add_item(ui.TextDisplay("# Changelog"))

    for category, lines in entries.items():
        container.add_item(ui.Separator())
        container.add_item(ui.TextDisplay(f"### {category}"))
        for line in lines:
            container.add_item(ui.TextDisplay(f"- {line}"))

    return container
```

```python
changelog = build_changelog({
    "Fixed": ["Ticket buttons not closing on mobile", "Avatar thumbnails caching stale images"],
    "Added": ["New /luxorx stats command", "Kaleshi support role auto-assign"],
})
```

## Notes

- `spacing` accepts `SeparatorSpacing.small` or `SeparatorSpacing.large`.
- Overusing separators between every single line makes a layout feel choppy — reserve them for actual section breaks.
- A `Separator` still counts toward the 40-component cap, so heavily sectioned layouts can hit that limit faster than you'd expect.

---

Previous: [← Text Display](Text-Display.md) · Next: [Section & Thumbnail →](Section-And-Thumbnail.md)
