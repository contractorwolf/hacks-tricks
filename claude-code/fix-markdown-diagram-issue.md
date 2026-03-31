# Fix: Broken Box-Drawing Diagrams in Markdown

## The Problem

When Claude Code generates ASCII/Unicode box-drawing diagrams by hand (using characters like `┌ ─ ┐ │ └ ┘`), it frequently miscounts column positions. This results in **broken boxes** where corners don't meet edges:

```
┌──────────────┐      ┌──────────────┐
│ Host (Bridge) │      │   MCP Server   │
│ (chat client) │      │  (your tools)  │
└──────────────┘      └────────────────┘
       ▲ broken              ▲ broken
```

The top/bottom borders (`┌───┐` / `└───┘`) end up a different width than the content rows (`│ text │`), so the right-side corners and walls are misaligned. This is especially common when:

- Box content varies in length
- Unicode characters (arrows `◄►`, box-drawing chars) are multi-byte but single-width
- Multiple boxes sit side-by-side with spacing between them
- Diagrams wrap across rows with vertical connectors

The issue was caught visually in a markdown preview and required **two rounds of manual fixes** before all boxes were correct — proving that hand-editing is unreliable for this.

## The Solution

**Generate diagrams programmatically instead of hand-typing them.**

Use a script (Python, Node, etc.) to calculate box widths and assemble the diagram. The script guarantees that the top border, content rows, and bottom border of every box use exactly the same width.

### Example: Python Box Diagram Generator

```python
# Define the content for each box
words = ["betty boughter", "bought some", "butter, but", "she said"]

COLS = 4          # boxes per row before wrapping
ARROW = "────►"   # horizontal connector between boxes
ARROW_LEN = 5

# Each box is individually sized to fit its content
def box_pad(text):
    return len(text) + 2  # 1 space padding on each side

for i, word in enumerate(words):
    w = box_pad(word)

    # All three rows use the same width `w`, so corners always align
    top = "┌" + "─" * w + "┐"   # ┌──────────┐
    mid = "│" + word.center(w) + "│"  # │  word    │
    bot = "└" + "─" * w + "┘"   # └──────────┘
```

### Key Principles

1. **One variable controls width.** The top border, content, and bottom border of each box all use the same `w` value. There is no opportunity for them to diverge.

2. **Per-box sizing.** Each box is sized to its own content (`len(text) + padding`), not a fixed width. A box containing `"to make"` is narrower than one containing `"betty boughter"`.

3. **Connectors are computed, not hand-spaced.** Horizontal arrows (`────►`) and vertical connectors (`│` / `▼`) are placed by calculating cumulative column positions, not by counting spaces visually.

4. **Snake-pattern wrapping.** When rows exceed `COLS` boxes, even rows reverse their display order and use `◄────` arrows, with vertical connectors dropping from the correct side (right on odd rows, left on even rows).

### Full Generator (Variable-Width, Snake-Wrap)

```python
words = "betty boughter bought some butter, but she said".split()

# Group words into pairs (2 words per box)
pairs = []
for i in range(0, len(words), 2):
    pairs.append(" ".join(words[i:i+2]))

COLS = 4
ARROW = "────►"
ARROW_REV = "◄────"
ARROW_LEN = 5

def box_pad(text):
    return len(text) + 2

rows = [pairs[i:i+COLS] for i in range(0, len(pairs), COLS)]

for row_idx, row_pairs in enumerate(rows):
    is_reverse = (row_idx % 2 == 1)
    display = list(reversed(row_pairs)) if is_reverse else list(row_pairs)

    # Top border — width matches content exactly
    top = ""
    for i, p in enumerate(display):
        w = box_pad(p)
        top += "┌" + "─" * w + "┐"
        if i < len(display) - 1:
            top += " " * ARROW_LEN

    # Content row — same width variable `w`
    mid = ""
    for i, p in enumerate(display):
        w = box_pad(p)
        mid += "│" + p.center(w) + "│"
        if i < len(display) - 1:
            mid += ARROW_REV if is_reverse else ARROW

    # Bottom border — same width variable `w`
    bot = ""
    for i, p in enumerate(display):
        w = box_pad(p)
        bot += "└" + "─" * w + "┘"
        if i < len(display) - 1:
            bot += " " * ARROW_LEN

    print(top)
    print(mid)
    print(bot)

    # Vertical connector to next row
    if row_idx < len(rows) - 1:
        if is_reverse:
            col = (box_pad(display[0]) + 2) // 2
        else:
            total = sum(box_pad(p) + 2 for p in display) + (len(display) - 1) * ARROW_LEN
            col = total - (box_pad(display[-1]) + 2) // 2 - 1
        print(" " * col + "│")
        print(" " * col + "▼")
```

### Output

```
┌────────────────┐     ┌─────────────┐     ┌─────────────┐     ┌──────────┐
│ betty boughter │────►│ bought some │────►│ butter, but │────►│ she said │
└────────────────┘     └─────────────┘     └─────────────┘     └──────────┘
```

Every `┌` lines up with its `└`. Every `┐` lines up with its `┘`. Guaranteed by code, not by counting.

## How to Persist This as a Claude Code Memory

To ensure Claude Code always generates diagrams this way, a feedback memory was saved:

**File:** `~/.claude/projects/<project>/memory/feedback_ascii_box_diagrams.md`

```markdown
---
name: Verify ASCII box diagrams
description: When writing Unicode/ASCII box-drawing diagrams, always verify column
  alignment of all corners and edges before presenting
type: feedback
---

When creating ASCII/Unicode box-drawing diagrams, always verify that every box
completes its loop — all four corners must align vertically and horizontally.

**Why:** Miscounted columns when hand-writing a diagram, causing box edges to not
meet up. The user had to ask to fix it twice.

**How to apply:** After writing any box-drawing diagram, count the column positions
of each corner/wall character to confirm alignment. Ideally generate the diagram
programmatically (e.g., via a quick python snippet) rather than hand-typing it, to
eliminate manual counting errors.
```

This memory is loaded automatically in future conversations, so Claude Code will default to scripted generation instead of hand-typing box diagrams.
