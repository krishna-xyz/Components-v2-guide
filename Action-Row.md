# ActionRow

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

`ui.ActionRow` is the container for interactive elements inside CV2 — buttons or a single select menu live here, the same role `ui.View` rows played in classic components.

## Buttons in a Row

```python
row = ui.ActionRow()
row.add_item(ui.Button(label="Approve", style=discord.ButtonStyle.success, custom_id="app_approve"))
row.add_item(ui.Button(label="Deny", style=discord.ButtonStyle.danger, custom_id="app_deny"))

container.add_item(row)
```

## Wiring Up Callbacks

```python
class ApplicationLayout(ui.LayoutView):
    def __init__(self, applicant: discord.Member):
        super().__init__(timeout=None)
        self.applicant = applicant

        container = ui.Container(accent_color=discord.Colour.blurple())
        container.add_item(ui.TextDisplay(f"## Staff Application — {applicant.display_name}"))

        row = ui.ActionRow()
        approve = ui.Button(label="Approve", style=discord.ButtonStyle.success, custom_id="app_approve")
        deny = ui.Button(label="Deny", style=discord.ButtonStyle.danger, custom_id="app_deny")
        approve.callback = self.on_approve
        deny.callback = self.on_deny
        row.add_item(approve)
        row.add_item(deny)
        container.add_item(row)

        self.add_item(container)

    async def on_approve(self, interaction: discord.Interaction):
        await self.applicant.add_roles(discord.utils.get(interaction.guild.roles, name="Staff"))
        await interaction.response.send_message(f"{self.applicant.mention} approved ✅", ephemeral=True)

    async def on_deny(self, interaction: discord.Interaction):
        await interaction.response.send_message(f"{self.applicant.mention} denied ❌", ephemeral=True)
```

## A Select Menu in a Row

Only one select menu is allowed per `ActionRow` (no mixing with buttons in that same row):

```python
select = ui.Select(
    placeholder="Pick a role color",
    options=[
        discord.SelectOption(label="Red Team", value="red", emoji="🔴"),
        discord.SelectOption(label="Blue Team", value="blue", emoji="🔵"),
    ],
    custom_id="team_picker"
)
select.callback = on_team_pick
container.add_item(ui.ActionRow(select))
```

## Notes

- Up to **5 buttons** per `ActionRow`, same limit as classic `ui.View` rows.
- A row holding a select menu can't also hold buttons — keep selects in their own row.
- `custom_id` values must be unique across the whole message; reusing one across two different rows will cause both to fire the same callback.

---

Previous: [← File Component](File-Component.md) · Next: [Buttons →](Buttons.md)
