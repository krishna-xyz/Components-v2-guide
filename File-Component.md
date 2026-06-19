# File Component

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

`ui.File` displays an attached file inline as its own block — handy for log dumps, exported transcripts, or config backups, instead of leaving the attachment to float awkwardly below the message.

## Basic Usage

```python
container = ui.Container()
container.add_item(ui.TextDisplay("# Ticket Transcript"))
container.add_item(ui.File(media="attachment://transcript.txt"))

layout = ui.LayoutView()
layout.add_item(container)

await channel.send(view=layout, file=discord.File("transcript.txt"))
```

## Real Example — Ticket Close With Transcript

```python
async def close_ticket(channel: discord.TextChannel, closer: discord.Member):
    transcript_path = await generate_transcript(channel)  # your own export logic

    container = ui.Container(accent_color=discord.Colour.dark_grey())
    container.add_item(ui.TextDisplay(f"## Ticket Closed by {closer.display_name}"))
    container.add_item(ui.TextDisplay("Transcript attached below for record-keeping."))
    container.add_item(ui.File(media="attachment://transcript.txt"))

    layout = ui.LayoutView()
    layout.add_item(container)

    log_channel = channel.guild.get_channel(LOG_CHANNEL_ID)
    await log_channel.send(view=layout, file=discord.File(transcript_path, filename="transcript.txt"))
```

## Multiple Files

You can place several `ui.File` blocks in one container — each one tied to its own attachment:

```python
container.add_item(ui.TextDisplay("# Backup Export — LuxorX Development"))
container.add_item(ui.File(media="attachment://config.json"))
container.add_item(ui.File(media="attachment://roles_backup.json"))
```

## Notes

- `ui.File` only works with `attachment://` references — it cannot point to a remote URL directly.
- Each referenced filename must exactly match a `discord.File` passed via `file=` or `files=` in the same send call.
- Useful distinction: use `MediaGallery` for images you want previewed visually, and `File` for documents or text dumps you want listed but not rendered as a picture.

---

Previous: [← Media Gallery](Media-Gallery.md) · Next: [Action Row →](Action-Row.md)
