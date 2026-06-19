# Section & Thumbnail

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

`ui.Section` groups one or more `TextDisplay` blocks with a single "accessory" on the side — either a `ui.Thumbnail` (small image) or a `ui.Button`. This is the direct replacement for `embed.set_thumbnail()` plus a field.

## Text + Thumbnail

```python
section = ui.Section(
    accessory=ui.Thumbnail(media="attachment://member_avatar.png")
)
section.add_item(ui.TextDisplay("**kaleshi#0001**"))
section.add_item(ui.TextDisplay("Member since Jan 2025 · 14,200 messages sent"))

container = ui.Container()
container.add_item(ui.TextDisplay("## Member Lookup"))
container.add_item(section)
```

## Text + Button (Action Card Pattern)

This is the pattern most LuxorX panels use — info on the left, a single action button on the right:

```python
class WarnCard(ui.Section):
    def __init__(self, case_id: int, user: str, reason: str):
        button = ui.Button(label="Revoke Warning", style=discord.ButtonStyle.danger, custom_id=f"revoke_{case_id}")
        button.callback = self.revoke
        super().__init__(accessory=button)
        self.case_id = case_id
        self.add_item(ui.TextDisplay(f"**Case #{case_id}** — {user}"))
        self.add_item(ui.TextDisplay(f"Reason: {reason}"))

    async def revoke(self, interaction: discord.Interaction):
        await interaction.response.send_message(f"Case #{self.case_id} revoked.", ephemeral=True)
```

```python
container = ui.Container(accent_color=discord.Colour.red())
container.add_item(ui.TextDisplay("# Active Warnings"))
container.add_item(WarnCard(case_id=88, user="kaleshi#0001", reason="Spam"))
```

## Using a Remote Image as Thumbnail

```python
section = ui.Section(
    accessory=ui.Thumbnail(media="https://cdn.luxorx.dev/badges/booster.png")
)
section.add_item(ui.TextDisplay("**New Booster!**\nThanks for supporting the server 💜"))
```

## Notes

- A `Section` can hold up to 3 `TextDisplay` children — beyond that, split into a separate `Section` or plain text block.
- Only **one** accessory is allowed per `Section` — pick either a `Thumbnail` or a `Button`, not both.
- Local image attachments must use the `attachment://filename.png` syntax and the file must be attached to the same `send_message`/`send` call.

---

Previous: [← Separator](Separator.md) · Next: [Media Gallery →](Media-Gallery.md)
