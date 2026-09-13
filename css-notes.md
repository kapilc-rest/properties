# My CSS Notes

> Add a line here every time you look something up. First time: look it up. Second time: look it up again. Third time: you shouldn't need to.

## Units

- `px` — fixed size, doesn't scale
- `%` — relative to parent element
- `vw` / `vh` — relative to viewport width/height (1vw = 1% of viewport width)
- `rem` — relative to root (`html`) font size
- `em` — relative to parent's font size
- `ch` — relative to the width of the `0` character in the current font. Useful for capping text line-length in a readable range (see Functions section below).

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

- Defined with `--name: value;` (kebab-case, case-sensitive, no spaces)
- Used with `var(--name)`
- Usually declared on `:root` so they're global — `:root` is basically the same as `html`, but with higher specificity
- **Scope**: a custom property is only usable on the selector it's declared on, plus that selector's descendants — not siblings or ancestors. Declaring `--main-bg` inside `.card` means only `.card` and things nested inside it can use `var(--main-bg)`.
- **Fallback values**: `var()` takes an optional 2nd argument used if the property is invalid or undeclared — `color: var(--undeclared, black);`. Fallbacks can nest: `var(--a, var(--b, yellow))` tries `--a`, then `--b`, then falls back to `yellow`.

**Theming pattern**: declare two sets of the same variable names under different scopes (e.g. `.dark { --bg: black; }` / `.light { --bg: white; }`), then toggle the class on `html`/`body`. Everything referencing `var(--bg)` updates automatically. Can also respond to the OS-level theme setting with a media query:
```css
:root {
  --bg: white; /* default/light theme */
}
@media (prefers-color-scheme: dark) {
  :root {
    --bg: black;
  }
}
```
Note: `prefers-color-scheme` only supports `light`/`dark` (no custom theme names), and doesn't let the user override it manually — it just reflects the OS/browser setting.

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

**Flexbox vs Position**: flexbox arranges elements *in relation to each other* (rows, columns, spacing) and stays responsive as content/screen size changes. `position` places one specific element precisely, possibly overlapping others, but requires manual pixel values and doesn't adapt automatically. Real layouts use both — flexbox for overall structure, `position: absolute` for the occasional badge/overlay inside a flex container.

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

- `calc()` — do math in CSS, and can **mix units** in one expression, e.g. `width: calc(100% - 20px);`. Can also be nested: `calc(100vh - calc(var(--header) + var(--footer)))`.
- `min()` — takes a comma-separated list, returns the **smallest**. Good for responsive sizing: `width: min(150px, 100%);` means "150px, but never wider than the parent." Can do basic math inline without needing `calc()`: `width: min(80ch, 100vw - 2rem);`
- `max()` — same idea, returns the **largest**. Useful for accessibility — ensures something doesn't shrink below a usable size even if the viewport is tiny or the user has zoomed in.
- `clamp(min, preferred, max)` — takes 3 values: a floor, a flexible "ideal" value (often a viewport-relative unit like `vw`), and a ceiling. E.g. `font-size: clamp(1.5rem, 5vw, 3rem);` scales the font with the viewport width but never goes below 1.5rem or above 3rem.

**Practical pattern — readable line width**: readable text blocks are usually 45–75 characters wide. `width: clamp(45ch, 50%, 75ch);` keeps a paragraph at 50% of its container by default, but never narrower than 45 characters or wider than 75 — using the `ch` unit means the limits scale with the font itself, not just the viewport.

**Accessibility warning**: capping a font's max size with `max()` or `clamp()` can technically block users from zooming text up to 200%, which fails WCAG 1.4.4 (Resize Text). Worth testing with the browser's zoom before relying on it for headline/title sizing.

**Other useful CSS functions** (from the full MDN list — most CSS functions are pretty niche, these are the ones worth knowing):

- `rgb()` / `hsl()` — define colors by red/green/blue or hue/saturation/lightness (+ optional alpha for transparency)
- `linear-gradient()` / `radial-gradient()` — generate a gradient image, usable as a `background`
- `color-mix(in srgb, red 50%, blue)` — blend two colors together, handy for hover states derived from a base color
- `translate()` / `rotate()` / `scale()` — move, rotate, or resize an element via the `transform` property, without affecting layout flow (great for animations/hover effects since they don't trigger reflow the way changing `top`/`left`/`width` does)
- `attr()` — pulls an HTML attribute's value into CSS, most commonly with `content` in `::before`/`::after` (e.g. showing a link's `href` next to it)
- `url()` — references an external resource (image, font, SVG) — `background-image: url("photo.jpg");`
- `repeat()` / `minmax()` — used inside `grid-template-columns` to avoid repeating yourself and to set flexible column/row sizes with a floor and ceiling

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
- `z-index` has **no effect on `static` elements** — an element needs `position: relative/absolute/fixed/sticky` before `z-index` does anything. This is also why setting `position: relative;` on an element (even with no `top`/`left` values) makes it render on top of any `static` siblings by default.

**Why use `absolute` instead of `static`**: `static` elements can never overlap — they just flow one after another. `absolute` lets you layer things on top of other content (badges on a card corner, tooltips, dropdown menus, full-bleed overlays on an image) without disturbing the normal layout around them, and gives exact pixel placement that `static` can't.

**Common mix-up**: `absolute` and `fixed` both pull the element out of flow, but they anchor to different things. `absolute` → nearest positioned ancestor (or the whole page if none exists). `fixed` → always the browser viewport, which is why sticky headers/navbars use `fixed`, not `absolute` — you want them tracking the screen, not some parent container.

**Gotchas (from MDN):**
- `sticky` needs at least one of `top`/`right`/`bottom`/`left` set to something other than `auto`, or it just behaves like `relative` (no sticking).
- `sticky` sticks to its nearest **scrolling ancestor** (an ancestor with `overflow: hidden/scroll/auto`) — not necessarily the whole page.
- Any non-`static` position value creates a new **stacking context**, which affects how `z-index` layers work with siblings elsewhere on the page.
- `fixed`/`sticky` elements have to be repainted by the browser on every scroll frame, which can cause jank (stutter) on slower devices — worth keeping an eye on if a sticky/fixed element feels laggy.

**When to use `fixed` vs `sticky`**: use `fixed` when something should stay in the exact same spot on screen no matter what (a chat bubble button, a floating "back to top" link). Use `sticky` when something should scroll normally, then stop once it hits an edge and stay in view while its section scrolls past (a section heading, a sidebar nav, a table header). The trade-off: `fixed` elements can quietly cover other content since they're totally out of flow — easy to miss on mobile where screen space is tight; `sticky` elements disappear once their parent container scrolls out of view, since they never leave that parent.

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

