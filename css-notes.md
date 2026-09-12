# My CSS Notes

> Add a line here every time you look something up. First time: look it up. Second time: look it up again. Third time: you shouldn't need to.

## Units

- `px` — fixed size, doesn't scale
- `%` — relative to parent element
- `vw` / `vh` — relative to viewport width/height (1vw = 1% of viewport width)
- `rem` — relative to root (`html`) font size
- `em` — relative to parent's font size

## Media Queries

```css
@media (max-width: 768px) {
  /* styles for screens 768px and smaller */
}
```

- `max-width` → applies when screen is **this size or smaller**
- `min-width` → applies when screen is **this size or larger**
- Common breakpoints to start with: mobile (~480px), tablet (~768px), desktop (~1024px)

## Variables (Custom Properties)

```css
:root {
  --main-color: #333;
}

.box {
  color: var(--main-color);
}
```

- Defined with `--name: value;`
- Used with `var(--name)`
- Usually declared on `:root` so they're global

## Flexbox

```css
.container {
  display: flex;
  justify-content: center; /* horizontal alignment */
  align-items: center;     /* vertical alignment */
  flex-direction: row;     /* or column */
  gap: 1rem;
}
```

## Grid

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
}
```

## Selectors

- `.class` — class selector
- `#id` — id selector
- `element` — tag selector
- `element.class` — element with class
- `parent > child` — direct child only
- `parent child` — any descendant

## Functions

- `calc()` — do math in CSS, e.g. `width: calc(100% - 20px);`
- `clamp(min, preferred, max)` — responsive value with limits
- `min()` / `max()` — pick smallest/largest of given values

## Box Model

- `content` → `padding` → `border` → `margin` (inside to outside)
- `box-sizing: border-box;` makes padding/border count *inside* the width (usually what you want)

## Specificity (why my CSS isn't applying)

Higher specificity wins, regardless of order in the file:

1. Inline styles (`style="..."`) — highest
2. IDs (`#id`)
3. Classes, attributes, pseudo-classes (`.class`, `[type="text"]`, `:hover`)
4. Elements/tags (`div`, `p`) — lowest
5. `!important` overrides everything (avoid using it — it's a last resort, makes debugging harder later)

If two rules have equal specificity, the one **later in the file** wins.

## Position

```css
position: static;   /* default — normal flow, top/left/etc do nothing */
position: relative;  /* offsets from its own normal position, still takes up original space */
position: absolute;  /* removed from flow, positioned relative to nearest positioned ancestor */
position: fixed;     /* positioned relative to the viewport, stays put on scroll */
position: sticky;    /* acts relative until a scroll threshold, then sticks like fixed */
```

- `absolute` needs a `relative` (or other non-static) ancestor to position against — otherwise it positions against the whole page.

## Pseudo-classes & Pseudo-elements

- `:hover`, `:focus`, `:active` — interaction states
- `:first-child`, `:last-child`, `:nth-child(n)` — target by position among siblings
- `::before`, `::after` — inject generated content (needs `content: "";` to show)

```css
.item::before {
  content: "→ ";
}
```

## Random Gotchas / Things I Learned the Hard Way

- (add stuff here as you hit weird bugs)

