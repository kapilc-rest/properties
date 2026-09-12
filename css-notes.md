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

Pseudo-classes (single colon) target elements that already exist based on state or position. Pseudo-elements (double colon) target parts of the page that aren't real HTML elements.

**Note on colons**: older resources (like Shay Howe's site, written pre-CSS3) sometimes use a single colon for pseudo-elements too (`:before` instead of `::before`). Browsers still support the single-colon form for backwards compatibility, but `::` is the current standard — that's what's used everywhere below.

### Pseudo-classes

- `:hover`, `:focus`, `:active` — interaction states
- `:link`, `:visited` — unvisited vs. visited links
- `:enabled`, `:disabled` — form inputs that are/aren't available for interaction
- `:checked` — a checked checkbox or radio button
- `:indeterminate` — a checkbox/radio that's neither checked nor unchecked (a "partial" UI state, usually set via JS)
- `:first-child`, `:last-child` — first/last element among its siblings, regardless of type
- `:only-child` — the only element inside its parent (no siblings at all)
- `:first-of-type`, `:last-of-type`, `:only-of-type` — like the `-child` versions, but only counts siblings **of the same element type**. e.g. `p:first-of-type` finds the first `<p>` in a parent even if other elements (headings, divs) come before it
- `:nth-child(n)` — the nth element counting **all** sibling types from the start
- `:nth-last-child(n)` — same idea, but counts from the **end** of the parent instead
- `:nth-of-type(n)`, `:nth-last-of-type(n)` — like `nth-child`, but only counts siblings of the same type
- `:empty` — an element with zero children and zero text content (not even whitespace)
- `:not(selector)` — negation, selects anything that does NOT match
- `:target` — matches an element whose `id` matches the current URL's `#fragment` (e.g. clicking a link to `page.html#section2` lets you style `#section2:target`)
- `:root` — top of the document (basically `html`), the usual home for CSS variables

**`nth-child()` expression cheatsheet**: `n` is a counter starting at 0. `2n` = every 2nd element (even), `2n+1` = odd, `3n` = every 3rd, a plain number like `4` = just the 4th element. `-n+5` = the first 5 elements (counts backwards from 5).

### Pseudo-elements

- `::before`, `::after` — inject generated content (needs `content: "";` to show)
- `::marker` — style list bullets/numbers
- `::first-letter`, `::first-line` — style just the first letter/line of text
- `::selection` — style highlighted/selected text (only `color`, `background`/`background-color`, and `text-shadow` work here — `background-image` is ignored)

```css
.item::before {
  content: "→ ";
}
```

## Attribute Selectors

```css
[src] { }              /* has the attribute, any value */
img[src] { }            /* combine with an element */
[src="puppy.jpg"] { }   /* exact match */
[class^="aus"] { }      /* ^= starts with */
[src$=".jpg"] { }       /* $= ends with */
[for*="ill"] { }        /* *= contains, anywhere in the string */
[rel~="tag"] { }        /* ~= one word in a space-separated list matches exactly, e.g. rel="tag nofollow" */
[lang|="en"] { }        /* |= exact match OR starts with value followed by a hyphen, e.g. lang="en-US" */
```

## Selector Reference (from CSS Diner)

All selector types used across CSS Diner's 32 levels:

| Selector | Meaning |
|---|---|
| `*` | Universal — selects everything |
| `element` | Type selector — e.g. `plate` |
| `.class` | Class selector |
| `#id` | ID selector |
| `element.class` | Element with a specific class |
| `A, B` | Comma combinator — selects A **and** B |
| `A B` | Descendant selector — B inside A, any depth |
| `A > B` | Child selector — B is a **direct** child of A |
| `A + B` | Adjacent sibling — B immediately follows A |
| `A ~ B` | General sibling — B follows A at any distance (same parent) |
| `[attr]` | Attribute selector — has the attribute |
| `[attr="value"]` | Attribute equals value |
| `:first-child` | First child of its parent |
| `:last-child` | Last child of its parent |
| `:only-child` | The only child of its parent |
| `:nth-child(n)` | The nth child (n can be a number, `even`, `odd`, or a formula like `3n`) |
| `:first-of-type` | First element of its type among siblings |
| `:not(selector)` | Negation — selects everything that does NOT match |

## Random Gotchas / Things I Learned the Hard Way

- (add stuff here as you hit weird bugs)

