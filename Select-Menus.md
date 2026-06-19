# Select Menus

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

Select menus work the same as in classic components — `ui.Select` for a string dropdown, plus the typed variants (`UserSelect`, `RoleSelect`, `ChannelSelect`, `MentionableSelect`). Inside CV2, they live in their own `ActionRow`.

## String Select

```python
select = ui.Select(
    placeholder="Choose your region",
    custom_id="region_select",
    options=[
        discord.SelectOption(label="NA", value="na", emoji="🌎"),
        discord.SelectOption(label="EU", value="eu", emoji="🌍"),
        discord.SelectOption(label="ASIA", value="asia", emoji="🌏"),
    ]
)
select.callback = on_region_pick
container.add_item(ui.ActionRow(select))
```

```python
async def on_region_pick(interaction: discord.Interaction):
    region = interaction.data["values"][0]
    await interaction.response.send_message(f"Region set to **{region.upper()}**.", ephemeral=True)
```

## Role Select (Self-Assign Panel)

```python
class RoleSelectLayout(ui.LayoutView):
    def __init__(self):
        super().__init__(timeout=None)

        container = ui.Container(accent_color=discord.Colour.green())
        container.add_item(ui.TextDisplay("# Pick Your Notification Roles"))

        role_select = ui.RoleSelect(
            placeholder="Select roles to add",
            custom_id="notif_roles",
            min_values=0,
            max_values=3
        )
        role_select.callback = self.on_roles_picked
        container.add_item(ui.ActionRow(role_select))

        self.add_item(container)

    async def on_roles_picked(self, interaction: discord.Interaction):
        roles = interaction.data["resolved"]["roles"]
        await interaction.user.add_roles(*[interaction.guild.get_role(int(r)) for r in roles])
        await interaction.response.send_message(f"Added {len(roles)} role(s).", ephemeral=True)
```

## Multi-Select with a Max Cap

```python
select = ui.Select(
    placeholder="Pick up to 2 game tags",
    custom_id="game_tags",
    min_values=1,
    max_values=2,
    options=[
        discord.SelectOption(label="Valorant", value="val"),
        discord.SelectOption(label="Minecraft", value="mc"),
        discord.SelectOption(label="Fortnite", value="fn"),
    ]
)
```

## Notes

- Only **one** select menu fits per `ActionRow`, and it can't be combined with buttons in that same row.
- `min_values=0` lets a user submit an empty selection — useful for "remove all my roles" style menus.
- `interaction.data["values"]` gives you the chosen string values; typed selects (`RoleSelect`, `UserSelect`, etc.) instead populate `interaction.data["resolved"]`.

---

Previous: [← Buttons](Buttons.md) · Next: [Real-World Examples →](Real-World-Examples.md)
