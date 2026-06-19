# MediaGallery

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

`ui.MediaGallery` displays one or more images in a responsive grid — the direct upgrade from `embed.set_image()`, except it supports more than one image at a time.

## Basic Usage

```python
gallery = ui.MediaGallery()
gallery.add_item(media="attachment://render_1.png", description="Front view")
gallery.add_item(media="attachment://render_2.png", description="Side view")

container = ui.Container()
container.add_item(ui.TextDisplay("# New Build Preview — LuxorX Development"))
container.add_item(gallery)

await channel.send(
    view=ui.LayoutView().add_item(container) and container,  # see note below on attaching files
    files=[discord.File("render_1.png"), discord.File("render_2.png")]
)
```

> Cleaner version of the send call above:

```python
layout = ui.LayoutView()
layout.add_item(container)

await channel.send(
    view=layout,
    files=[discord.File("render_1.png"), discord.File("render_2.png")]
)
```

## Mixing Local and Remote Images

```python
gallery = ui.MediaGallery()
gallery.add_item(media="attachment://screenshot.png")            # local file
gallery.add_item(media="https://cdn.luxorx.dev/banner.png")        # remote URL
```

## Real Example — "Spotted in #showcase" Bot Feature

```python
async def repost_to_showcase(message: discord.Message):
    images = [a for a in message.attachments if a.content_type.startswith("image/")]
    if not images:
        return

    gallery = ui.MediaGallery()
    for img in images[:5]:  # Discord caps gallery items per message
        gallery.add_item(media=img.url, description=img.filename)

    container = ui.Container(accent_color=discord.Colour.teal())
    container.add_item(ui.TextDisplay(f"## Spotted from {message.author.display_name}"))
    container.add_item(gallery)

    layout = ui.LayoutView()
    layout.add_item(container)
    await message.channel.send(view=layout)
```

## Notes

- A `MediaGallery` supports up to **10 items**, same as Discord's standard image grid limit.
- `description` shows as alt-text on hover — good practice for accessibility.
- If you're attaching local files, the `discord.File` objects must be passed via `files=` in the same send call, and the `attachment://` name must match the filename exactly.

---

Previous: [← Section & Thumbnail](Section-And-Thumbnail.md) · Next: [File Component →](File-Component.md)
