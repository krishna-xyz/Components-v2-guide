# Real-World Examples

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

Full, combined patterns pulled together from the individual component pages — closer to what you'd actually ship in a production bot.

## 1. Full Ticket System (Open → Claim → Close)

```python
import discord
from discord import ui

class TicketOpenLayout(ui.LayoutView):
    def __init__(self):
        super().__init__(timeout=None)
        container = ui.Container(accent_color=discord.Colour.green())
        container.add_item(ui.TextDisplay("# LuxorX Support"))
        container.add_item(ui.TextDisplay("Need help? Pick a category to open a private ticket."))

        select = ui.Select(
            placeholder="Select a category",
            custom_id="ticket_category",
            options=[
                discord.SelectOption(label="Billing", value="billing", emoji="💳"),
                discord.SelectOption(label="Bug Report", value="bug", emoji="🐛"),
                discord.SelectOption(label="General", value="general", emoji="💬"),
            ]
        )
        select.callback = self.on_open
        container.add_item(ui.ActionRow(select))
        self.add_item(container)

    async def on_open(self, interaction: discord.Interaction):
        category = interaction.data["values"][0]
        channel = await interaction.guild.create_text_channel(f"ticket-{interaction.user.name}")
        await channel.send(view=TicketRoomLayout(category, interaction.user))
        await interaction.response.send_message(f"Ticket created: {channel.mention}", ephemeral=True)


class TicketRoomLayout(ui.LayoutView):
    def __init__(self, category: str, opener: discord.Member):
        super().__init__(timeout=None)
        self.opener = opener

        container = ui.Container(accent_color=discord.Colour.blurple())
        container.add_item(ui.TextDisplay(f"## Ticket — {category.title()}"))
        container.add_item(ui.TextDisplay(f"Opened by {opener.mention}. A staffer will claim this shortly."))

        row = ui.ActionRow()
        claim = ui.Button(label="Claim", style=discord.ButtonStyle.primary, custom_id="ticket_claim")
        close = ui.Button(label="Close", style=discord.ButtonStyle.danger, custom_id="ticket_close")
        claim.callback = self.on_claim
        close.callback = self.on_close
        row.add_item(claim)
        row.add_item(close)
        container.add_item(row)
        self.add_item(container)

    async def on_claim(self, interaction: discord.Interaction):
        await interaction.response.send_message(f"Claimed by {interaction.user.mention}.", ephemeral=False)

    async def on_close(self, interaction: discord.Interaction):
        await interaction.response.send_message("Closing ticket... transcript saved.", ephemeral=False)
        await interaction.channel.delete(delay=5)
```

---

## 2. Server Dashboard (Stats + Quick Actions)

```python
class DashboardLayout(ui.LayoutView):
    def __init__(self, guild: discord.Guild):
        super().__init__(timeout=None)
        self.guild = guild

        container = ui.Container(accent_color=discord.Colour.from_rgb(88, 101, 242))
        container.add_item(ui.TextDisplay(f"# {guild.name} — Dashboard"))
        container.add_item(ui.TextDisplay(f"**Members:** {guild.member_count}\n**Boosts:** {guild.premium_subscription_count}"))
        container.add_item(ui.Separator())

        row = ui.ActionRow()
        refresh = ui.Button(label="Refresh", style=discord.ButtonStyle.secondary, custom_id="dash_refresh")
        announce = ui.Button(label="New Announcement", style=discord.ButtonStyle.primary, custom_id="dash_announce")
        refresh.callback = self.on_refresh
        announce.callback = self.on_announce
        row.add_item(refresh)
        row.add_item(announce)
        container.add_item(row)
        self.add_item(container)

    async def on_refresh(self, interaction: discord.Interaction):
        await interaction.response.edit_message(view=DashboardLayout(self.guild))

    async def on_announce(self, interaction: discord.Interaction):
        await interaction.response.send_modal(AnnouncementModal())


class AnnouncementModal(ui.Modal, title="New Announcement"):
    body = ui.TextInput(label="Message", style=discord.TextStyle.paragraph, max_length=1000)

    async def on_submit(self, interaction: discord.Interaction):
        container = ui.Container(accent_color=discord.Colour.gold())
        container.add_item(ui.TextDisplay(f"# Announcement\n{self.body.value}"))
        layout = ui.LayoutView()
        layout.add_item(container)
        await interaction.response.send_message(view=layout)
```

---

## 3. Leaderboard with Pagination

```python
class LeaderboardLayout(ui.LayoutView):
    def __init__(self, ranked: list[tuple[str, int]], page: int = 0):
        super().__init__(timeout=180)
        self.ranked = ranked
        self.page = page
        self._build()

    def _build(self):
        self.clear_items()
        start = self.page * 10
        chunk = self.ranked[start:start + 10]

        container = ui.Container(accent_color=discord.Colour.gold())
        container.add_item(ui.TextDisplay(f"## Leaderboard — Page {self.page + 1}"))
        container.add_item(ui.Separator())

        for i, (name, score) in enumerate(chunk, start=start + 1):
            container.add_item(ui.TextDisplay(f"**#{i}** {name} — {score} pts"))

        nav = ui.ActionRow()
        if self.page > 0:
            prev_btn = ui.Button(label="◀", style=discord.ButtonStyle.secondary, custom_id="lb_prev")
            prev_btn.callback = self.go_prev
            nav.add_item(prev_btn)
        if start + 10 < len(self.ranked):
            next_btn = ui.Button(label="▶", style=discord.ButtonStyle.secondary, custom_id="lb_next")
            next_btn.callback = self.go_next
            nav.add_item(next_btn)
        if nav.children:
            container.add_item(nav)

        self.add_item(container)

    async def go_prev(self, interaction: discord.Interaction):
        self.page -= 1
        self._build()
        await interaction.response.edit_message(view=self)

    async def go_next(self, interaction: discord.Interaction):
        self.page += 1
        self._build()
        await interaction.response.edit_message(view=self)
```

---

These patterns are deliberately verbose so you can lift whole blocks straight into your own bot. Swap branding, colors, and `custom_id` prefixes for your own project.

---

Previous: [← Select Menus](Select-Menus.md) · Next: [Tips & Gotchas →](Tips-And-Gotchas.md)
