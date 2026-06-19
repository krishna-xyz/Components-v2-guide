# TextDisplay

[← Back to main guide](README.md)

> Part of the LuxorX Development Components V2 guide • Support: [discord.gg/kaleshi](https://discord.gg/kaleshi)

`ui.TextDisplay` renders a block of text with full Discord markdown support — headers, bold/italic, lists, blockquotes, and mentions all work exactly like they do in a normal message.

## Basic Usage

```python
container.add_item(ui.TextDisplay("# LuxorX Announcements"))
container.add_item(ui.TextDisplay("**Maintenance window:** Saturday 2 AM – 4 AM UTC"))
```

## Multiple TextDisplays vs. One Big String

You *can* cram everything into one `TextDisplay` with `\n`, but splitting into separate `TextDisplay` items gives Discord cleaner spacing and makes your code easier to edit piece by piece:

```python
# Works, but harder to maintain
container.add_item(ui.TextDisplay("# Rules\n1. Be respectful\n2. No spam\n3. Follow ToS"))

# Cleaner — each rule is its own block
container.add_item(ui.TextDisplay("# Rules"))
container.add_item(ui.TextDisplay("1. Be respectful"))
container.add_item(ui.TextDisplay("2. No spam"))
container.add_item(ui.TextDisplay("3. Follow ToS"))
```

## Dynamic Text

Since it's just a string, building text dynamically from data is straightforward:

```python
def build_profile_text(member: discord.Member) -> str:
    return (
        f"**{member.display_name}**\n"
        f"Joined: {member.joined_at.strftime('%b %d, %Y')}\n"
        f"Roles: {len(member.roles) - 1}"
    )

container.add_item(ui.TextDisplay(build_profile_text(member)))
```

## Notes

- Each `TextDisplay` has its own character limit identical to a normal Discord message (2000 characters) — split long content across multiple `TextDisplay`s instead of relying on one giant block.
- Mentions (`<@id>`, `<#id>`) and custom emoji render normally inside `TextDisplay`.
- There's no `TextDisplay.edit()` shortcut — to change text, rebuild the layout and resend with `edit_message`/`edit_original_response`.

---

Previous: [← Container](Container.md) · Next: [Separator →](Separator.md)
