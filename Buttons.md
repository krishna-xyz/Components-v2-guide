# Buttons

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord](https://discord.gg/bethakk)

Buttons in CV2 are the same `ui.Button` class you already know — the only difference is they're placed inside an `ui.ActionRow`, which itself sits inside a `Container`, instead of attaching directly to a `View`.

## Button Styles

```python
ui.Button(label="Primary", style=discord.ButtonStyle.primary, custom_id="b1")
ui.Button(label="Secondary", style=discord.ButtonStyle.secondary, custom_id="b2")
ui.Button(label="Success", style=discord.ButtonStyle.success, custom_id="b3")
ui.Button(label="Danger", style=discord.ButtonStyle.danger, custom_id="b4")
ui.Button(label="Link", style=discord.ButtonStyle.link, url="https://discord.gg/kaleshi")
```

## A Confirmation Pattern

A common pattern: disable buttons after click so the user can't double-fire an action.

```python
class ConfirmPurge(ui.LayoutView):
    def __init__(self, amount: int):
        super().__init__(timeout=60)
        self.amount = amount

        container = ui.Container(accent_color=discord.Colour.red())
        container.add_item(ui.TextDisplay(f"## Delete {amount} messages?"))

        row = ui.ActionRow()
        confirm = ui.Button(label="Confirm", style=discord.ButtonStyle.danger, custom_id="purge_confirm")
        cancel = ui.Button(label="Cancel", style=discord.ButtonStyle.secondary, custom_id="purge_cancel")
        confirm.callback = self.on_confirm
        cancel.callback = self.on_cancel
        row.add_item(confirm)
        row.add_item(cancel)
        container.add_item(row)

        self.add_item(container)
        self.row = row

    async def on_confirm(self, interaction: discord.Interaction):
        for child in self.row.children:
            child.disabled = True
        await interaction.response.edit_message(view=self)
        await interaction.channel.purge(limit=self.amount)

    async def on_cancel(self, interaction: discord.Interaction):
        for child in self.row.children:
            child.disabled = True
        await interaction.response.edit_message(view=self)
```

## Link Buttons (No Callback Needed)

```python
support_row = ui.ActionRow(
    ui.Button(label="Join Kaleshi", style=discord.ButtonStyle.link, url="https://discord.gg/kaleshi"),
    ui.Button(label="LuxorX Dev Docs", style=discord.ButtonStyle.link, url="https://luxorx.dev/docs")
)
container.add_item(support_row)
```

## Notes

- `link` style buttons never trigger an interaction — Discord just opens the URL, so they don't need a `callback` or `custom_id`.
- `disabled=True` is the cleanest way to prevent double-clicks; you don't need to remove the row entirely.
- Emoji-only buttons work too: `ui.Button(emoji="✅", style=discord.ButtonStyle.success, custom_id="quick_yes")`.

---

Previous: [← Action Row](Action-Row.md) · Next: [Select Menus →](Select-Menus.md)
