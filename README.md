# Discord.py Components V2 — The LuxorX Guide

> Guide by **krishna.xyz** • Dev House: **LuxorX Development** • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

Components V2 (CV2) is Discord's newer message layout system. Instead of stuffing everything into a single `embed`, you build a message out of stackable layout blocks — text, separators, sections, galleries, files, and action rows — and you get pixel-level control over how the final message reads on both desktop and mobile.

This guide walks through every CV2 building block with fresh, original examples (a bot dashboard, a moderation panel, and a ticket system) rather than copy-pasted snippets, so you can see the pieces used in realistic scenarios.

---

## Why bother with CV2 over plain embeds?

- Embeds are one rigid box. CV2 lets you compose many small boxes.
- Full markdown works inside `TextDisplay` — headers, bold, lists, quotes.
- Buttons and selects can sit *inline* next to text instead of floating below everything.
- You can mix images, files, and text in any order you want.
- Catch: `content`, `embeds`, `stickers`, and `poll` are **mutually exclusive** with CV2 components — pick one system per message.

---

## The Building Blocks

| Component | Purpose |
|---|---|
| `ui.LayoutView` | Root container — every CV2 message starts here |
| `ui.Container` | A visual block, optionally with a colored left-edge accent |
| `ui.TextDisplay` | Markdown-capable text |
| `ui.Section` | Pairs text with a thumbnail or a button on the side |
| `ui.Separator` | Divider line, with optional spacing-only mode |
| `ui.MediaGallery` | Grid of images |
| `ui.File` | Inline file attachment block |
| `ui.ActionRow` | Holds buttons or a select menu |

---

## Setup

CV2 needs the GitHub build of discord.py, not the PyPI release:

```bash
pip install git+https://github.com/Rapptz/discord.py
```

---

## Example 1 — A Bot Status Panel

```python
import discord
from discord import ui

class StatusPanel(ui.LayoutView):
    def __init__(self, guild_count: int, latency_ms: int):
        super().__init__(timeout=None)

        container = ui.Container(accent_color=discord.Colour.from_rgb(88, 101, 242))
        container.add_item(ui.TextDisplay("# LuxorX Status"))
        container.add_item(ui.TextDisplay(
            f"**Servers:** {guild_count}\n**Latency:** {latency_ms}ms"
        ))
        container.add_item(ui.Separator())

        row = ui.ActionRow()
        refresh = ui.Button(label="Refresh", style=discord.ButtonStyle.secondary, custom_id="luxorx_refresh")
        refresh.callback = self.on_refresh
        row.add_item(refresh)
        container.add_item(row)

        self.add_item(container)

    async def on_refresh(self, interaction: discord.Interaction):
        await interaction.response.send_message("Pulled fresh stats. — krishna.xyz", ephemeral=True)
```

Send it like any other view:

```python
await interaction.response.send_message(view=StatusPanel(guild_count=412, latency_ms=87))
```

---

## Example 2 — Moderation Case Card (Section + Thumbnail)

`ui.Section` is the block to reach for when you want text sitting next to an avatar or a button.

```python
section = ui.Section(
    accessory=ui.Thumbnail(media="attachment://avatar.png")
)
section.add_item(ui.TextDisplay("**Case #482**\nUser: kaleshi#0001"))
section.add_item(ui.TextDisplay("**Reason:** Spamming invite links"))

container = ui.Container(accent_color=discord.Colour.red())
container.add_item(ui.TextDisplay("## Moderation Log — discord.gg/kaleshi"))
container.add_item(section)
```

---

## Example 3 — Image Gallery for a Showcase Channel

```python
gallery = ui.MediaGallery()
gallery.add_item(media="attachment://screenshot_1.png", description="Before")
gallery.add_item(media="attachment://screenshot_2.png", description="After")

container = ui.Container()
container.add_item(ui.TextDisplay("# LuxorX Development — Build Showcase"))
container.add_item(gallery)
```

---

## Example 4 — Ticket Open/Close Flow with a Select Menu

```python
class TicketLayout(ui.LayoutView):
    def __init__(self):
        super().__init__(timeout=None)

        container = ui.Container(accent_color=discord.Colour.green())
        container.add_item(ui.TextDisplay("# Open a Support Ticket"))
        container.add_item(ui.TextDisplay("Pick a category below to get routed to the right team."))

        select = ui.Select(
            placeholder="Choose a category",
            options=[
                discord.SelectOption(label="Billing", value="billing"),
                discord.SelectOption(label="Bug Report", value="bug"),
                discord.SelectOption(label="General Question", value="general"),
            ],
            custom_id="luxorx_ticket_select"
        )
        select.callback = self.on_select
        container.add_item(ui.ActionRow(select))

        self.add_item(container)

    async def on_select(self, interaction: discord.Interaction):
        category = interaction.data["values"][0]
        await interaction.response.send_message(
            f"Ticket opened under **{category}**. A LuxorX staffer will be with you shortly.",
            ephemeral=True
        )
```

---

## Embeds → CV2 Cheat Sheet

| Old Embed | CV2 Equivalent |
|---|---|
| `embed.title` | `ui.TextDisplay("# Title")` |
| `embed.description` | `ui.TextDisplay("text")` |
| `embed.add_field()` | `ui.Section` with stacked `TextDisplay`s |
| `embed.set_image()` | `ui.MediaGallery` |
| `embed.set_thumbnail()` | `ui.Section(accessory=ui.Thumbnail(...))` |
| `embed.color` | `ui.Container(accent_color=...)` |

---

## Pagination Pattern

A reusable paginator container — handy for leaderboard or log dumps.

```python
class PageView(ui.LayoutView):
    def __init__(self, entries: list[str], page: int = 0, per_page: int = 5):
        super().__init__(timeout=180)
        self.entries = entries
        self.page = page
        self.per_page = per_page
        self._render()

    def _render(self):
        start = self.page * self.per_page
        chunk = self.entries[start:start + self.per_page]

        container = ui.Container()
        container.add_item(ui.TextDisplay(f"## Page {self.page + 1} — LuxorX Records"))
        container.add_item(ui.Separator())

        for entry in chunk:
            container.add_item(ui.TextDisplay(entry))

        nav = ui.ActionRow()
        if self.page > 0:
            back = ui.Button(label="◀ Back", style=discord.ButtonStyle.secondary, custom_id="page_back")
            back.callback = self.go_back
            nav.add_item(back)
        if start + self.per_page < len(self.entries):
            fwd = ui.Button(label="Next ▶", style=discord.ButtonStyle.secondary, custom_id="page_fwd")
            fwd.callback = self.go_fwd
            nav.add_item(fwd)
        if nav.children:
            container.add_item(nav)

        self.clear_items()
        self.add_item(container)

    async def go_back(self, interaction: discord.Interaction):
        self.page -= 1
        self._render()
        await interaction.response.edit_message(view=self)

    async def go_fwd(self, interaction: discord.Interaction):
        self.page += 1
        self._render()
        await interaction.response.edit_message(view=self)
```

---

## Limits & Gotchas

- A single message can hold at most **40 total components**, counting nested ones inside containers.
- CV2 and classic message `content` / `embeds` / `stickers` / `polls` cannot be combined in the same send call.
- `ui.View` still works fine for plain button bots — CV2 is opt-in per message, not a forced rewrite.
- Always preview your layout on mobile; sections wrap differently on narrow screens than they do on desktop.
- Custom IDs must stay unique per message if you're routing multiple callbacks through one interaction listener.

---

## Table of Contents

| # | Guide | Description |
|---|---|---|
| 1 | [LayoutView](Layout-View.md) | The root of every CV2 message |
| 2 | [Container](Container.md) | Grouping components with optional accent color |
| 3 | [TextDisplay](Text-Display.md) | Rendering markdown text |
| 4 | [Separator](Separator.md) | Visual dividers and spacing |
| 5 | [Section & Thumbnail](Section-And-Thumbnail.md) | Side-by-side text + image or button |
| 6 | [MediaGallery](Media-Gallery.md) | Image grids |
| 7 | [File Component](File-Component.md) | Inline file attachments |
| 8 | [ActionRow](Action-Row.md) | Container for buttons and selects |
| 9 | [Buttons](Buttons.md) | Styles, confirmation flows, link buttons |
| 10 | [Select Menus](Select-Menus.md) | String, role, and typed selects |
| 11 | [Real-World Examples](Real-World-Examples.md) | Full ticket system, dashboard, leaderboard |
| 12 | [Tips & Gotchas](Tips-And-Gotchas.md) | Limits, mistakes, and best practices |

---

<p align="center">
Maintained by <b>krishna.xyz</b> · Built under <b>LuxorX Development</b><br>
Questions or contributions? Drop by <a href="https://discord.gg/kaleshi">discord.gg/kaleshi</a>
</p>
