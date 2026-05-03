# 🎨 CSS Quick Reference Cheat Sheet

---

## 📦 THE BOX MODEL

Every element is a box with 4 layers:

```
┌─────────────────────────────────────────┐
│               MARGIN                    │  ← Space OUTSIDE the border
│   ┌─────────────────────────────────┐   │
│   │            BORDER               │   │  ← The visible border line
│   │   ┌─────────────────────────┐   │   │
│   │   │         PADDING         │   │   │  ← Space INSIDE the border
│   │   │   ┌─────────────────┐   │   │   │
│   │   │   │    CONTENT      │   │   │   │  ← Actual text/image
│   │   │   │  width × height │   │   │   │
│   │   │   └─────────────────┘   │   │   │
│   │   └─────────────────────────┘   │   │
│   └─────────────────────────────────┘   │
└─────────────────────────────────────────┘
```

```css
/* box-sizing: content-box (default) — width = content only */
/* box-sizing: border-box  (recommended) — width = content + padding + border */

* { box-sizing: border-box; } /* USE THIS ALWAYS */
```

---

## 📐 DISPLAY PROPERTY

| Value          | Behavior                                      |
|----------------|-----------------------------------------------|
| `block`        | Full width, stacks vertically                 |
| `inline`       | Wraps with text, no width/height              |
| `inline-block` | Wraps with text, BUT respects width/height    |
| `flex`         | Flexbox container                             |
| `grid`         | Grid container                                |
| `none`         | Hidden, removed from layout                   |

```
block:          [====FULL WIDTH====]
                [====FULL WIDTH====]

inline:         [A][B][C] wraps like text

inline-block:   [A][B][C] but you can set width/height
```

---

## 🔀 FLEXBOX

> **When to use:** Lay out items in a **single row OR column**. Great for navbars, centering, cards in a row, aligning buttons.

### Flex Container vs Flex Items
```
Parent (display: flex)
┌──────────────────────────────────────┐
│  [Item 1]  [Item 2]  [Item 3]        │
└──────────────────────────────────────┘
     ↑ These are flex items
```

### flex-direction — which way items flow
```css
flex-direction: row;            /* → default */
flex-direction: row-reverse;    /* ← */
flex-direction: column;         /* ↓ */
flex-direction: column-reverse; /* ↑ */
```
```
row:             [1] → [2] → [3]
column:          [1]
                  ↓
                 [2]
                  ↓
                 [3]
```

### justify-content — alignment along MAIN AXIS
```
(for row direction, main axis = horizontal →)

flex-start:    [1][2][3]·········
flex-end:      ·········[1][2][3]
center:        ····[1][2][3]····
space-between: [1]····[2]····[3]
space-around:  ·[1]··[2]··[3]·
space-evenly:  ··[1]··[2]··[3]··
```

### align-items — alignment along CROSS AXIS
```
(for row direction, cross axis = vertical ↕)

         ┌──────────────────┐
stretch: │[tall][tall][tall]│ ← fills height (default)
         └──────────────────┘

         ┌──────────────────┐
center:  │  [1] [2] [3]    │ ← centered vertically
         └──────────────────┘

         ┌──────────────────┐
start:   │[1][2][3]         │ ← top aligned
         └──────────────────┘

         ┌──────────────────┐
end:     │         [1][2][3]│ ← bottom aligned
         └──────────────────┘
```

### flex-wrap
```css
flex-wrap: nowrap;   /* all in one line (may overflow) */
flex-wrap: wrap;     /* items wrap to next line */
```
```
nowrap:  [1][2][3][4][5]  ← squishes or overflows

wrap:    [1][2][3]
         [4][5]           ← wraps to new line
```

### flex item properties
```css
flex-grow: 1;    /* item grows to fill space */
flex-shrink: 1;  /* item shrinks if needed */
flex-basis: 200px; /* default size before grow/shrink */
flex: 1;         /* shorthand: grow=1, shrink=1, basis=0 */

align-self: center; /* overrides align-items for THIS item */
order: 2;           /* change visual order */
```

### 🎯 Common Flexbox Recipes

```css
/* Perfect centering (both axes) */
.parent {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Navbar: logo left, links right */
.nav {
  display: flex;
  justify-content: space-between;
  align-items: center;
}

/* Equal width cards */
.card { flex: 1; }

/* Push last item to right */
.last-item { margin-left: auto; }
```

---

## 🔲 CSS GRID

> **When to use:** Complex **two-dimensional** layouts (rows AND columns together). Great for page layouts, image galleries, dashboards.

```
display: grid — Parent becomes grid container
┌─────┬─────┬─────┐
│  1  │  2  │  3  │  ← row 1
├─────┼─────┼─────┤
│  4  │  5  │  6  │  ← row 2
└─────┴─────┴─────┘
  col1  col2  col3
```

### Defining Columns & Rows
```css
grid-template-columns: 200px 200px 200px;  /* 3 fixed cols */
grid-template-columns: 1fr 2fr 1fr;        /* fractional */
grid-template-columns: repeat(3, 1fr);     /* 3 equal cols */
grid-template-rows: 100px auto 100px;      /* header/main/footer */
gap: 16px;                                 /* space between cells */
column-gap: 16px;  row-gap: 8px;          /* separate gaps */
```

### Placing Items
```css
/* Item spanning multiple cells */
grid-column: 1 / 3;   /* from line 1 to line 3 (spans 2 cols) */
grid-row: 1 / 2;      /* row 1 only */
grid-column: span 2;  /* span 2 columns from current position */
```

```
grid-column: 1 / 3 means:

line: 1    2    3    4
      ┃         ┃    ┃
      ┣━━━━━━━━━┫    ┃   ← item occupies col 1 to col 3
      ┃         ┃    ┃
```

### 🎯 Common Grid Recipes
```css
/* Classic page layout */
.page {
  display: grid;
  grid-template-columns: 250px 1fr;
  grid-template-rows: 60px 1fr 40px;
  grid-template-areas:
    "header  header"
    "sidebar content"
    "footer  footer";
}
.header  { grid-area: header; }
.sidebar { grid-area: sidebar; }
.content { grid-area: content; }
.footer  { grid-area: footer; }
```

```
Visual result:
┌──────────────────────────┐
│         HEADER           │
├────────┬─────────────────┤
│SIDEBAR │     CONTENT     │
├────────┴─────────────────┤
│         FOOTER           │
└──────────────────────────┘
```

---

## 📜 OVERFLOW

> Controls what happens when content is **too big** for its container.

```
Container is 100px × 100px,
Content is 200px tall:

┌──────────┐
│ This is  │
│ inside   │  ← fits in box
│ the box  │
│          │
└──────────┘
  ...but what happens to the rest?
```

### overflow (shorthand for both X and Y)
```css
overflow: visible;  /* default — content spills out */
overflow: hidden;   /* cuts off overflow content */
overflow: scroll;   /* always shows scrollbars */
overflow: auto;     /* scrollbar only when needed ✅ recommended */
overflow: clip;     /* like hidden but no scroll programmatically */
```

```
visible:          hidden:           auto/scroll:
┌──────┐          ┌──────┐          ┌──────┐
│ text │          │ text │          │ text │↑
│ text │          │ text │          │ text │
│ text │          └──────┘          │ text │↓
│ text │ ← spills    ← cut off      └──────┘
│ text │                              ↑ scrollbar
```

---

## ↔️ overflow-x

> Controls overflow on the **horizontal axis** (left ↔ right).

```css
overflow-x: visible;  /* content spills left/right */
overflow-x: hidden;   /* hides horizontal overflow (clips) */
overflow-x: scroll;   /* horizontal scrollbar always shown */
overflow-x: auto;     /* horizontal scrollbar only when needed */
```

### When to use overflow-x
```
USE overflow-x: hidden when:
  - You have animations sliding in from sides
  - Prevent horizontal page scroll on mobile

USE overflow-x: auto / scroll when:
  - Wide tables that shouldn't shrink
  - Code blocks
  - Horizontal card carousels

  ┌─────────────────────────┐
  │  [Card 1][Card 2][Card 3]→→→[Card 4][Card 5]  │
  └─────────────────────────┘
  Scroll →→→→→→→→→→→→→→→→→→
```

```css
/* Horizontal scrollable row of cards */
.card-row {
  display: flex;
  overflow-x: auto;
  gap: 16px;
}
```

---

## ↕️ overflow-y

> Controls overflow on the **vertical axis** (top ↕ bottom).

```css
overflow-y: visible;  /* content spills up/down */
overflow-y: hidden;   /* hides vertical overflow */
overflow-y: scroll;   /* vertical scrollbar always shown */
overflow-y: auto;     /* vertical scrollbar only when needed ✅ */
```

### When to use overflow-y
```
USE overflow-y: auto when:
  - Sidebar with many nav items
  - Chat message list
  - Modal with long content

USE overflow-y: hidden when:
  - Fixed hero sections
  - Prevent body scroll when modal is open

  ┌───────────┐
  │ Message 1 │ ↑
  │ Message 2 │
  │ Message 3 │
  │ Message 4 │ scroll
  │ Message 5 │
  │ Message 6 │ ↓
  └───────────┘
```

```css
/* Scrollable chat window */
.chat {
  height: 400px;
  overflow-y: auto;
}

/* Prevent body scroll when modal open */
body.modal-open {
  overflow-y: hidden;
}
```

---

## ⚠️ overflow-x + overflow-y Gotcha

> Setting one to `hidden` or `scroll` **forces** the other to `auto` if it was `visible`.

```css
/* This: */
overflow-x: hidden;
overflow-y: visible;

/* Becomes: */
overflow-x: hidden;
overflow-y: auto;   /* browser changes visible → auto! */
```

---

## 📍 POSITION

| Value      | Relative to              | In flow? |
|------------|--------------------------|----------|
| `static`   | Normal flow (default)    | ✅ Yes   |
| `relative` | Its own normal position  | ✅ Yes   |
| `absolute` | Nearest positioned parent| ❌ No    |
| `fixed`    | Viewport (screen)        | ❌ No    |
| `sticky`   | Scroll position          | ✅ Yes   |

```
static/relative:
┌─────────────────┐
│ [A] [B] [C]     │  ← normal flow
└─────────────────┘

absolute (parent must have position: relative):
┌─────────────────┐
│         [B]     │
│  [A]            │  ← B pulled out, A & C don't know B exists
│           [C]   │
└─────────────────┘

fixed:
┌─────────────────┐
│ [NAVBAR]        │  ← stays on screen while you scroll
├─────────────────┤
│  scrollable     │
│  content...     │
└─────────────────┘

sticky:
Scrolls normally UNTIL it hits top: 0,
then sticks like fixed.
```

```css
/* Absolute positioning recipe */
.parent { position: relative; }
.child  {
  position: absolute;
  top: 0; right: 0;   /* top-right corner of parent */
}

/* Sticky header */
.header {
  position: sticky;
  top: 0;
  z-index: 100;
}
```

---

## 🎯 Z-INDEX

> Controls stacking order. Higher = on top. Only works on positioned elements (not static).

```
z-index: 3  →  [MODAL]        ← on top
z-index: 2  →  [OVERLAY]
z-index: 1  →  [NAVBAR]
z-index: 0  →  [PAGE CONTENT] ← bottom
```

---

## 📏 UNITS QUICK REFERENCE

| Unit  | Meaning                               | Use when                        |
|-------|---------------------------------------|---------------------------------|
| `px`  | Absolute pixels                       | Borders, shadows, exact sizes   |
| `%`   | Relative to parent                    | Responsive widths                |
| `em`  | Relative to current font-size         | Padding/margin around text      |
| `rem` | Relative to root font-size (html)     | Font sizes, consistent spacing  |
| `vw`  | % of viewport width                   | Full-width sections             |
| `vh`  | % of viewport height                  | Full-screen hero sections       |
| `fr`  | Fraction of grid space                | Grid columns only               |
| `ch`  | Width of "0" character                | Input/text max-width            |

---

## 📱 MEDIA QUERIES

```css
/* Mobile first approach (recommended) */
/* Base styles = mobile */

@media (min-width: 640px)  { /* sm — tablet portrait */ }
@media (min-width: 768px)  { /* md — tablet landscape */ }
@media (min-width: 1024px) { /* lg — laptop */ }
@media (min-width: 1280px) { /* xl — desktop */ }

/* Example */
.container {
  flex-direction: column; /* mobile: stack */
}
@media (min-width: 768px) {
  .container {
    flex-direction: row;  /* tablet+: side by side */
  }
}
```

---

## 🌊 FLEX vs GRID — When to Choose?

```
Use FLEXBOX when:
  ✅ Navigation bar
  ✅ Centering one item
  ✅ Button groups
  ✅ Items in a single row/column
  ✅ Content-driven sizing

  [logo] ←→ [nav] [nav] [nav] [btn]

Use GRID when:
  ✅ Page layout (header/sidebar/content)
  ✅ Image galleries
  ✅ Card grids with strict alignment
  ✅ 2D layout (rows AND columns matter)

  ┌──┬──┬──┐
  ├──┼──┼──┤
  └──┴──┴──┘
```

---

## 🔧 USEFUL SNIPPETS

```css
/* Responsive image */
img { max-width: 100%; height: auto; }

/* Center anything */
.center {
  display: flex;
  justify-content: center;
  align-items: center;
}

/* Full viewport page */
.page { min-height: 100vh; }

/* Text truncation with ellipsis */
.truncate {
  white-space: nowrap;
  overflow: hidden;
  text-overflow: ellipsis;
}

/* Multi-line text clamp */
.clamp-3 {
  display: -webkit-box;
  -webkit-line-clamp: 3;
  -webkit-box-orient: vertical;
  overflow: hidden;
}

/* Scrollable sidebar */
.sidebar {
  height: 100vh;
  overflow-y: auto;
  position: sticky;
  top: 0;
}

/* Prevent scroll when modal open */
.no-scroll { overflow: hidden; }

/* Smooth scrolling */
html { scroll-behavior: smooth; }

/* Custom scrollbar */
::-webkit-scrollbar { width: 8px; }
::-webkit-scrollbar-thumb { background: #888; border-radius: 4px; }
```

---

## 🎨 CSS CUSTOM PROPERTIES (Variables)

```css
:root {
  --color-primary: #3b82f6;
  --spacing-md: 16px;
  --font-size-lg: 1.125rem;
}

.button {
  background: var(--color-primary);
  padding: var(--spacing-md);
  font-size: var(--font-size-lg);
}
```

---

*💡 Tip: Use browser DevTools (F12) → Elements → Computed to debug layout issues visually.*
