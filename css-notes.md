# My CSS Notes

> Add a line here every time you look something up. First time: look it up. Second time: look it up again. Third time: you shouldn't need to.

## Units

- `px` — fixed size, doesn't scale
- `%` — relative to parent element
- `vw` / `vh` — relative to viewport width/height (1vw = 1% of viewport width)
- `rem` — relative to root (`html`) font size

**HTML attribute vs CSS property**: `width`/`height` written as HTML attributes on `<img>` (`<img width="300">`) only accept unitless numbers, always pixels — no `rem`/`%`/other units allowed there. As a CSS property (`img { width: 20rem; }`) any unit works normally. Worth keeping the HTML attributes set (even alongside CSS sizing) since they let the browser reserve the image's space before it loads, preventing layout shift — CSS alone doesn't give that early hint.
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

**More gotchas (from MDN):**
- `var()` can't be used in a media/container query **condition** (`@media (min-width: var(--bp))` doesn't work) — only as a property *value*.
- A fallback can itself contain commas: `var(--foo, red, blue)` treats everything after the first comma as one fallback value — handy when the fallback needs to be comma-separated, like a `font-family` list.
- If a `var()` substitution results in an invalid value for that property (e.g. a number where a color is expected), the browser falls back to the property's normal initial/inherited value — same as any other invalid CSS declaration.

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

**Gotcha — `flex-direction` vs `flex-wrap`**: `flex-direction` only accepts `row`/`row-reverse`/`column`/`column-reverse` — `wrap` is not a valid value for it. Wrapping is a *separate* property, `flex-wrap: wrap;` (default is `nowrap`, which keeps all items on one line even if they overflow). Writing `flex-direction: wrap;` is silently ignored, leaving items squeezed into a single row/column that can overflow its container.

**Gotcha — flex items won't shrink below their content by default**: flex items have an implicit `min-height: auto` (or `min-width: auto` in a row layout) — meaning their minimum size defaults to whatever their content naturally needs, not `0`. So `max-height`/`flex-shrink` on a flex item can cap its *growth*, but won't actually force it smaller than its content until that default minimum is overridden with `min-height: 0;` (or `min-width: 0;` for row layouts). Often paired with `overflow: auto;` so any content that still doesn't fit scrolls instead of forcing the container to grow anyway.

## Grid

```css
.container {
  display: grid;
  grid-template-columns: repeat(3, 1fr);
  gap: 1rem;
}
```

## Browser Compatibility

- Different browsers use different rendering engines — Chrome/Chromium-based browsers (Edge, Brave, etc.) use **Blink**, Safari uses **WebKit**, Firefox uses **Gecko**. A feature can work in one and not another.
- **[caniuse.com](https://caniuse.com)** — check before using a newer CSS feature to see which browsers/versions support it. Good habit: check this before relying on something unfamiliar.
- **iOS is a special case**: every browser on iOS/iPadOS (Chrome, Firefox, etc.) is required to use Safari's WebKit engine under the hood, regardless of branding. So "does it work in Safari" effectively covers all iOS browsers — but desktop Safari and mobile Safari aren't identical either, so test both if targeting Apple users.
- **Emulating devices in dev tools only mimics screen size** — it doesn't reproduce the actual OS/browser engine quirks of a real device. Something that looks fine in an emulated iPhone view in Chrome could still behave differently on an actual iPhone.

## Frameworks and Preprocessors

**Frameworks** (Bootstrap, Tailwind, Bulma, Foundation) — pre-written CSS bundled into reusable classes, e.g. a `.btn` class that fully styles a button with zero custom CSS. Bootstrap-style frameworks hand you whole components (dropdowns, cards); Tailwind's "utility-first" approach gives tiny single-purpose classes instead. Downsides: sites built with the same framework tend to look similar, and learning a framework before CSS fundamentals are solid makes it hard to debug or override its styles later — better to get comfortable with vanilla CSS first.

**Preprocessors** (SASS, LESS, Stylus) — a language layered on top of CSS that compiles down to regular CSS, adding things like loops, conditionals, and nesting. Note: several of their historical selling points (variables, nesting) now exist natively in vanilla CSS, so the case for learning one is weaker than it used to be — only worth it for genuinely missing features.

**Native CSS nesting**: plain CSS now supports nesting selectors directly, no preprocessor required.
```css
.card {
  padding: 1rem;

  h2 {
    font-size: 1.5rem;
  }

  &:hover {
    border-color: blue;
  }
}
```
- `&` refers to the parent selector — needed for combining with pseudo-classes (`&:hover`) or another class on the same element (`&.featured`). A plain nested type selector like `h2` above is automatically treated as a descendant selector without needing `&`.
- Compiles down to regular flat CSS conceptually (`.card h2 { ... }`, `.card:hover { ... }`) — it's a writing convenience, not a new mechanism, and doesn't change how specificity is calculated.
- Baseline-supported in modern browsers now, but check caniuse.com if older-browser support matters, since it's a relatively recent native CSS feature (SASS has supported nesting for far longer via compilation).

## Forms

```html
<form action="example.com/path" method="post">
  <label for="first_name">First Name:</label>
  <input type="text" id="first_name" name="first_name" placeholder="Bob...">
</form>
```

- **`action`** — URL the form data gets sent to. **`method`** — `GET` (retrieving data, e.g. search) or `POST` (changing data, e.g. creating an account). With `GET`, submitted data literally appends to the URL as a query string — each field's `name=value` pair joined by `&`, e.g. `page.html?name=Thor&email=thor%40asgard.com` — which is exactly why `GET` is wrong for anything sensitive (passwords, personal data): it ends up visible in the URL, browser history, and server logs. `POST` sends the data in the request body instead, out of the URL.
- **`<label for="...">`** must match the input's `id` — clicking the label focuses the input, which matters for accessibility. Alternative pattern: wrap the `<label>` directly around its `<input>` instead of using `for`/`id` — this works too (and is common for checkboxes), though `for`/`id` is still considered the more explicit best practice.
```html
<label><input type="checkbox" name="subscribe"> Subscribe</label>
```
- **`placeholder` vs `value`** — easy to mix up since both put text inside the field: `value` is **real, pre-filled data** — if the form submits untouched, `value` is exactly what gets sent, and the user can edit/delete it like anything they typed themselves. `placeholder` is just a **greyed-out hint** that vanishes the instant typing starts and is **never submitted** — an empty field with only a placeholder showing submits as an empty string. Also, `placeholder` isn't a substitute for a real `<label>`, since it disappears on focus and isn't reliably read by all screen readers.
- **`name`** — the key sent with the value when the form submits (see Units/general notes — no `name` means that field's data is dropped entirely on submit).
- Form controls (input, select, etc.) also work fine **outside** a `<form>` — useful when JS just needs to grab a value without submitting anywhere.

**Input types**: `text`, `email` (mobile keyboard adds @, validates format), `password` (masks characters), `number` (rejects non-numeric input), `date` (renders a date picker).

**`inputmode` and `autocomplete`**: for data that's numeric-looking but shouldn't use `type="number"` (credit card numbers, PINs) — the spinner arrows are useless and it's too easy to accidentally bump the value — use `type="text" inputmode="numeric"` instead. This still triggers a numeric mobile keyboard without the unwanted spinner behavior. Pair with `autocomplete="cc-number"` (or other standard autocomplete values) to let the browser suggest previously saved values — something genuinely hard to replicate with custom JS.

**`<textarea>` vs `<input type="text">`**: both accept text, but they're not interchangeable for multi-line content. `input type="text"` always strips line breaks before submitting — even paragraphs typed into it come out as one continuous line, so it's structurally wrong for anything meant to span multiple sentences (comments, bios, feedback). `<textarea>` genuinely preserves line breaks, gives the user a draggable resize handle by default, and uses `rows`/`cols` (line height/character width) instead of `input`'s `size` (single-line character width), since it's a 2D block rather than one line. It also has a real closing tag — initial content goes *between* the tags as text, not in a `value` attribute like `input` uses.

```html
<input type="text" value="pre-filled">
<textarea>Pre-filled content goes here</textarea>
```

`<textarea>` also takes a `wrap` attribute: `soft` (default — rendered text wraps visually, but the *submitted* value stays unwrapped as typed), `hard` (both rendered and submitted text get wrapped; requires `cols` to be set), `off` (no visual wrapping at all). Resizability itself is controlled by CSS, not an HTML attribute — the `resize` property (`both` default, `horizontal`, `vertical`, `none`) on the `<textarea>` selector.

**Selection elements** — three common ways to let users pick from predefined options:
- **`<select>`** + `<option>` — dropdown, best for longer lists. `value` on each option is what gets submitted; if omitted, the option's own text content is used as the value. Add `selected` to one option to default it. Group related options with `<optgroup label="...">`. Add `multiple` to let users select more than one option at once (standard OS multi-select, e.g. Ctrl/Cmd-click) — this also changes the rendering from a collapsed dropdown to a listbox showing several options at once; `size` controls how many rows are visible.
- **Autocomplete box (`<datalist>`)**: pairs with a normal text input to offer suggested values without restricting input to only those choices (unlike `<select>`, users can still type something not in the list). Give the `<datalist>` an `id`, then point a `<input list="thatId">` at it:
```html
<input type="text" name="fruit" list="fruitOptions">
<datalist id="fruitOptions">
  <option>Apple</option>
  <option>Banana</option>
</datalist>
```
- **Radio buttons** (`type="radio"`) — best for 5 or fewer visible options. All radios sharing the same `name` become mutually exclusive (selecting one deselects the rest). `checked` sets the default.
- **Checkboxes** (`type="checkbox"`) — like radios, but multiple can be selected at once. A single checkbox is also the standard pattern for a true/false toggle (e.g. "subscribe to newsletter").

**Meter and progress bars** — visual, non-input elements for showing numeric values, not actually a data-entry control:
- **`<meter min="0" max="100" value="75" low="33" high="66" optimum="0">`** — represents a fixed value within a range (disk space used, a rating). `low`/`high`/`optimum` let the browser color-code it (green/yellow/red) based on how good the current value is.
- **`<progress max="100" value="75">`** — represents a value that changes over time toward a goal (file upload percentage, form completion). Simpler than `<meter>` — no color-coding logic, just a fill bar.

**Buttons** — the `<button>` element takes a `type`:
- `submit` (default if unspecified) — submits the form it's inside
- `reset` — clears all fields back to their initial values
- `button` — does nothing on its own, meant for JS-driven interactions

Gotcha: any button inside a `<form>` defaults to `type="submit"` — so a button meant only for JS (like a toggle) needs `type="button"` explicitly, or it'll accidentally submit the form too.

**Associating a submit button with a form**: nesting the button inside the `<form>` tags does this automatically — no extra attribute needed. If the button needs to live outside the `<form>` element in the markup (for layout/styling reasons), give the `<form>` an `id` and point the button at it with the `form` attribute instead:
```html
<form id="signupForm" action="/submit" method="post"></form>
<button type="submit" form="signupForm">Submit</button>
```

**Organizing forms** — `<fieldset>` groups related inputs together, `<legend>` gives that group a heading (must come right after the opening `<fieldset>` tag). Common for grouping a set of radio buttons under one question. Historically `<fieldset>` didn't support `display: flex` reliably across browsers, so some older guides fall back to `float` for laying out its contents — worth a quick check if a flex layout inside a fieldset misbehaves, though modern browser support has largely caught up.

**Styling challenges**: (1) every browser has different default form-control styles, so consistent cross-browser design requires overriding them yourself; (2) text-based inputs style easily like any element, but radio buttons/checkboxes are trickier to restyle, and some controls (like the native date picker calendar) can't be styled at all without rebuilding them in JS.

Widgets fall into three rough tiers, from MDN: **easy to style** — `<form>`, `<fieldset>`/`<legend>`, single-line text inputs, `<textarea>`, buttons, `<label>`, `<output>`. **Harder to style** — checkboxes, radio buttons, `<input type="search">`. **Internals that CSS alone can't touch** — `color`, date pickers, `range`, `file` (though the file-picker's button can be styled via `::file-selector-button` — just not the filename text next to it), and everything involved in `<select>`'s native dropdown list.

**Font inheritance gotcha**: form controls often don't inherit `font-family`/`font-size` from their parent by default — many browsers fall back to the OS's system font instead, which looks inconsistent with the rest of the page. Fix with:
```css
button, input, select, textarea {
  font-family: inherit;
  font-size: 100%;
}
```

**Consistent sizing across widget types**: each control has its own built-in border/padding/margin rules, so giving several different widgets (a text input, a select, a button) the same visual size takes `box-sizing: border-box` plus resetting padding/margin yourself:
```css
input, textarea, select, button {
  width: 150px;
  padding: 0;
  margin: 0;
  box-sizing: border-box;
}
```

**Positioning `<legend>`**: it defaults to sitting on top of the `<fieldset>`'s top border. To move it (e.g. to a bottom corner), position the `<fieldset>` as `relative` and the `<legend>` as `absolute` relative to it — this only changes the *visual* position; screen readers still announce it as the fieldset's label either way.
```css
fieldset { position: relative; }
legend { position: absolute; bottom: 0; right: 0; }
```

**The `appearance` property**: strips OS/system-level styling from a form control so you can build it up with CSS yourself. `appearance: none;` is by far the most-used value. On most text-like inputs the effect is subtle (mainly removes the stylized border), but on checkboxes/radios it's what makes real custom styling possible at all (see the checkbox technique below).

**`accent-color`**: a lighter-touch alternative to full `appearance: none;` restyling — changes just the primary tint color of checkboxes, radio buttons, and range sliders while keeping their native OS appearance (and therefore native forced-colors/high-contrast support) intact. Good enough when you only need brand-color tinting, not a fully custom look:
```css
input { accent-color: rebeccapurple; }
```

**Search input clear button**: some browsers keep the "×" clear icon visible on a `type="search"` field even after it loses focus (Safari), while others hide it (Chrome/Edge). To force it hidden consistently:
```css
input[type="search"]:not(:focus, :active)::-webkit-search-cancel-button {
  display: none;
}
```

**Custom `<select>` arrow**: `appearance: none;` removes the native dropdown arrow, but `::before`/`::after` don't work directly on `<select>` (its content is fully browser-controlled) — wrap it in a `<div>` and put the generated arrow on the wrapper instead:
```css
select { appearance: none; }
.select-wrapper { position: relative; }
.select-wrapper::after {
  content: "▼";
  position: absolute;
  right: 10px;
  top: 6px;
}
```
Even after that, the open dropdown's option list itself stays outside CSS's reach — you can inherit its font, but not control spacing/colors within it. Same limitation applies to `<datalist>`'s suggestion list. A `<select multiple>` sidesteps the problem entirely by showing all options inline instead of in a native popup.

**Per-control styling caveats worth remembering**:
- **`range`**: the track is stylable (`appearance: none` + custom `background`), but the draggable thumb needs non-standard, browser-specific pseudo-elements to restyle properly — genuinely fiddly, often not worth fully custom styling unless it's a design priority.
- **`color`**: reasonably cooperative — removing the border/padding gets you most of the way; anything beyond that needs a custom-built control.
- **`file`**: the picker button is stylable via `::file-selector-button`, but the "no file chosen"/filename text next to it is generated by the browser and can't be styled at all. The common workaround is visually hiding the real input (`opacity: 0`) and styling its `<label>` to look like a button instead, since clicking a label activates its linked input.
- **`date`/`datetime-local`/`time`/`month`/`week`**: the surrounding box styles like a normal text input, but the internal picker (calendar popup, increment spinner) is completely unstylable and immune to `appearance: none`.
- **`<meter>`/`<progress>`**: arguably the worst of the bunch — inconsistent height handling across browsers, you can set the background color but not the foreground fill color, and `appearance: none` makes things worse rather than better. Custom-built solutions (or a JS library) are usually the practical answer if precise styling matters.

**Custom checkbox technique**: `appearance: none;` strips almost all native browser styling from an input while keeping it fully interactive (keyboard support, `:focus` state stay intact) — this is what unlocks styling the checkbox yourself. From there you build the checked-state indicator using a `::before` pseudo-element, toggled visible via the `:checked` selector:

```css
input[type="checkbox"] {
  appearance: none;
  width: 1.2em;
  height: 1.2em;
  border: 0.15em solid currentColor;
  border-radius: 0.2em;
  display: grid;
  place-content: center;
}

input[type="checkbox"]::before {
  content: "";
  width: 0.7em;
  height: 0.7em;
  transform: scale(0);
  transition: transform 120ms ease-in-out;
  background-color: currentColor;
}

input[type="checkbox"]:checked::before {
  transform: scale(1);
}
```

Using `em` units and `currentColor` keeps the checkbox scaling with font size and inheriting the label's color automatically — handy for theming without repeating color values. Don't forget a visible `:focus` style (an `outline`) since that's a real accessibility requirement, not just a nice-to-have.

**More on text fields** (from MDN):
- `readonly` — user can't edit the value, but it's still submitted with the form.
- `disabled` — user can't edit it, and it's **not** submitted at all. (Different behavior from `readonly` — easy to mix up.)
- `size` — the input's physical on-screen width (in characters). `maxlength` — the max number of characters allowed to be typed in.
- `spellcheck` — turns the browser's spellcheck squiggles on/off for that field.
- Line breaks typed into a single-line text field get silently stripped before the data is sent — text inputs are genuinely single-line only.

**Hidden input**: `<input type="hidden" name="..." value="...">` — invisible to the user, never focusable, skipped by screen readers, but still submitted with the form. Used for things like a timestamp or an ID the page needs to pass along without the user seeing or editing it. Requires `name` and `value` to be useful; shouldn't have an associated `<label>`.

**Checkbox/radio submission behavior** — these work differently from text inputs:
- A checkbox/radio's value is only sent **if it's checked**. If unchecked, nothing is sent for it — not even its `name`.
- If checked but given no explicit `value` attribute, it's submitted as `name=on`.
- Once a radio button in a named group is checked, the user can't get back to "none selected" just by clicking — only a form reset clears it.

**`<button>` vs `<input type="submit/reset/button">`**: both produce working buttons with identical behavior, but `<button>` accepts HTML content between its tags (so you can bold text or add an icon inside it), while `<input>` buttons are void elements — their label is just plain text set via the `value` attribute. `<button>` is generally easier to style because of this.

**Image button**: `<input type="image" src="..." alt="...">` renders as a clickable image that submits the form like a submit button — but instead of sending a value, it sends the X/Y pixel coordinates of where the image was clicked, as `name.x` and `name.y`. Niche, but used for building visual "click map" style interactions.

**File picker**: `<input type="file">` lets users choose file(s) to upload. `accept="image/*"` restricts to a file type; add `multiple` to allow selecting more than one file at once.

**Attributes common to (almost) every form control**:

| Attribute | Default | What it does |
|---|---|---|
| `autofocus` | false | Auto-focuses this element on page load. Only one element per page should have it. |
| `disabled` | false | Blocks interaction and excludes the field from submission entirely. |
| `form` | — | Associates the control with a `<form>` elsewhere in the document by that form's `id`, useful when the control isn't nested inside the `<form>` tags. |
| `name` | — | The submitted data's key. |
| `value` | — | The control's initial value. |

### HTML5 input types (added after the original set)

- **`email`** — validates that the value looks like an email address before the form can submit; browsers show a built-in error otherwise. Add `multiple` to allow several comma-separated addresses in one field. Triggers the `@`-key mobile keyboard.
- **`search`** — functionally like `text`, but styled differently (rounded corners, often a clear/✕ button once there's a value), and browsers may auto-save/suggest previous search terms entered on the same site.
- **`tel`** — no format validation at all (phone number formats vary too much worldwide to enforce one), but still triggers the numeric keypad on mobile — useful for *any* numeric-style input where the `number` spinner UI isn't wanted, e.g. long ID/zip codes.
- **`url`** — validates that a protocol (like `https://`) is present and the format is well-formed. Doesn't check that the URL actually resolves to a real page.
- **`number`** — restricts to numeric input, shows spinner arrows, and adds `min`, `max`, `step` for constraining the range and increment. `step` defaults to `1` (whole numbers only) — use `step="0.01"` or `step="any"` for decimals. Best when the valid range is small (age, quantity); use `tel` instead for large ranges like ZIP codes where a spinner doesn't make sense.
- **`range`** — a slider version of `number`, for cases where the *exact* value matters less than roughly where it falls in a range. Also uses `min`/`max`/`step`. Doesn't show the current value on its own — pair it with an `<output for="id">` element and a bit of JS to display the live value as the user drags.
- **Date/time family** — `date` (year/month/day), `time` (24-hour value even if displayed as 12-hour), `datetime-local` (date + time, no timezone), `month`, `week`. All support `min`/`max`/`step` to constrain the range of selectable values.
- **`color`** — opens the OS's native color picker; the submitted value is always a lowercase 6-digit hex code (e.g. `#ff0000`).

**Client-side validation caveat**: types like `email`, `url`, and `number` validate in the browser before submission — genuinely helpful for user experience (catches typos immediately), but it is **not a security measure**. Client-side checks can be bypassed trivially (disabling JS, editing dev tools, sending a raw request), so the server must always re-validate any submitted data independently.

**Styling based on validation state**: the `:valid`/`:invalid` pseudo-classes let you style an input differently depending on whether its current value passes the browser's built-in validation (matches `type="email"`'s format, satisfies `required`, etc.) — useful for the classic red-border-on-bad-input pattern:
```css
input[type="email"]:invalid {
  border: 1px solid red;
}
```
Note: an empty-but-not-yet-touched required field also matches `:invalid` in most browsers, so this is often paired with `:focus`/`:not(:placeholder-shown)` to avoid showing an error before the user has even started typing.

### Form Validation (built-in constraints)

- **`required`** — field must have a value before the form submits. Always pair with a visible indicator (e.g. an asterisk on the label) so users know which fields are mandatory before they hit submit, not after. Matches the `:required` pseudo-class (useful for styling required fields distinctly, separate from valid/invalid state). For a group of same-named radio buttons, adding `required` to just one of them is enough to require the whole group — any radio in that group being checked satisfies it, not specifically the one with the attribute.
- **`novalidate`** — an attribute on `<form>` itself that turns off the browser's automatic validation UI (no popup bubbles, no submission blocking) — useful when building fully custom error messages with JavaScript instead. Note: it only disables the automatic *behavior*; the `:valid`/`:invalid` pseudo-classes and constraint checks are still available to use manually. **`formnovalidate`** does the same thing but scoped to a single submit/image button, rather than the whole form — handy for a "Save draft" button that should skip validation while the real "Submit" button still validates. Similarly, **`formaction`** on a submit/image button overrides the form's own `action` URL just for that button — useful when one form has multiple possible submit destinations (e.g. "Save" vs "Save and publish").
- **`minlength` / `maxlength`** — min/max character count for text-based fields. Gotcha: `minlength` does **not** imply `required` — an empty field still passes `minlength` validation and submits fine, since constraint validation for length only kicks in once the user has actually typed something.
- **`min` / `max`** — lower/upper bound for number-based controls (`number`, `range`, and the date/time family) — not usable on plain text fields. Each date/time type expects its own format for the value: `date` → `yyyy-mm-dd`, `month` → `yyyy-mm`, `week` → `yyyy-W##`, `time` → `HH:mm`, `datetime-local` → `yyyy-mm-ddTHH:mm`. Going out of range matches both `:invalid` and the more specific `:out-of-range` pseudo-class, so you can style range violations distinctly from other kinds of invalid input if it's useful. `<meter>`/`<progress>` also accept `min`/`max` (as plain attributes, not validation) — `<progress>`'s `max` defaults to `1` if omitted and must be a positive number.
- **`pattern`** — takes a regex the value must match, usable only on `<input>` elements (not `<textarea>`). Some types have pattern-like validation built in already (`email`, `url`). Writing your own regex from scratch is usually more trouble than it's worth — better to search for an established, tested pattern for the format you need (zip code, phone number, etc.) than hand-roll one. Pair with `placeholder` to show an example of the expected format, since the browser's default error message ("Please match the requested format") doesn't explain what's actually wrong.
- **`:user-valid` / `:user-invalid`** — like `:valid`/`:invalid`, but only activate **after the user has actually interacted with the field** (typed something, then blurred). This avoids the untouched-required-field problem `:invalid` has, without needing to layer on `:focus`/`:not(:placeholder-shown)` workarounds — generally the better default choice for validation styling.
- Built-in HTML validation is genuinely useful but has real limits — it can't check things like "does this password match the confirm-password field" or "is this username already taken." Anything beyond single-field format/range checks needs custom JavaScript validation, and server-side validation is still required regardless, since client-side checks can always be bypassed.

**Validation UX practices worth keeping in mind** (independent of the HTML/CSS mechanics above):
- Show each error next to its own field, not bundled together in one place at the top — users shouldn't have to hunt for which message belongs to which input.
- Don't disable the submit button while the form is "incomplete." Users may skip fields without realizing it; a disabled button gives no feedback on what's wrong. Let them click it and see the actual errors.
- Positive feedback (a checkmark on a valid field) is as useful as negative feedback — confirms progress, not just failure.
- Write error messages in plain language a user would say, not technical jargon ("Please match the requested format" tells them nothing useful).
- Don't rely on color alone (e.g. a red border) to flag an error — pair it with an icon or text too, for colorblind users.
- If password rules exist (length, symbols required, etc.), show them upfront before the user types, not only after they fail.
- The best validation is the one that prevents mistakes in the first place — good input types, formatting hints, and constraints reduce how often users hit an error at all.

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

## Opacity

`opacity` takes a number `0`–`1` (or a percentage) — `0` fully transparent, `1` (default) fully opaque. Applies to the **whole element and everything inside it as one unit** — a parent and its children all fade together relative to what's behind them, even if they'd otherwise have different opacities from each other. To fade just a background (not the content on top of it), use `background` with an alpha-channel color instead, e.g. `background: rgb(0 0 0 / 40%);`.

**Gotchas:**
- `opacity: 0` makes an element invisible, but it's **still in the DOM and still interactive** — it still registers clicks/hovers and can still receive keyboard focus if tabbable. If it should also be non-interactive, pair it with `pointer-events: none;` and remove it from the tab order, rather than relying on opacity alone.
- Setting `opacity` to anything other than `1` creates a new **stacking context** (same effect `position` has, covered above) — affects how `z-index` behaves with siblings.
- **Accessibility**: opacity is a purely visual effect — screen readers don't treat a faded element as hidden. Use the `hidden` attribute, or `visibility`/`display`, to actually hide something from assistive tech; reserve `opacity` for visual fading effects only. Also worth checking text contrast ratio when opacity is applied to text — faded text can fail WCAG contrast requirements even if it looks fine to someone with typical vision.
- `prefers-reduced-transparency` media query lets you respect a user's OS-level preference for less transparency, similar to `prefers-color-scheme`.

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
- `:focus-within` — matches a **parent** whose descendant currently has focus (e.g. highlight a whole form group when any of its inputs is focused)
- `:focus-visible` — matches only when focus is visually necessary (keyboard navigation), not on every mouse click — avoids showing a focus ring on mouse clicks while still showing it for keyboard users
- `:link`, `:visited` — unvisited vs. visited links
- `:enabled`, `:disabled` — form inputs that are/aren't available for interaction
- `:required`, `:optional` — has/doesn't have the `required` attribute
- `:read-only`, `:read-write` — has/doesn't have `readonly` set
- `:in-range`, `:out-of-range` — value is/isn't within a `min`/`max` bound (`number`/`range` inputs)
- `:default` — the default submit button/image in a form, or an option/checkbox/radio marked `selected`/`checked` on page load
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
- `::first-letter`, `::first-line` — style just the first letter/line of text. Note: there's no `::last-line` equivalent — CSS has no way to know how many lines something wraps into until render time, so there's no reliable "last line" concept to target natively (would need JS measurement or manual markup restructuring instead).
- `::selection` — style highlighted/selected text (only `color`, `background`/`background-color`, and `text-shadow` work here — `background-image` is ignored)
- `::placeholder` — styles an input's placeholder text specifically (e.g. `input[type="email"]::placeholder { color: blue; }`), independent of the actual typed value's styling

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

- `text-decoration-style: none;` doesn't work — `none` isn't a valid `text-decoration-style` value (only `solid`/`double`/`dotted`/`dashed`/`wavy` are). To remove a link's underline, use `text-decoration: none;` (the shorthand) or `text-decoration-line: none;` specifically. Note `text-decoration: none;` only removes the underline — the default link color (and separate `:visited` purple) needs its own override, e.g. `color: inherit;` to blend into surrounding text, or a specific `color` value.

