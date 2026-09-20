# My JS Notes

> Add a line here every time you look something up. First time: look it up. Second time: look it up again. Third time: you shouldn't need to.

## Array Methods (map, filter, sort, reduce)

- `arr.map(fn)` — returns a **new** array with the result of calling `fn` on each item. Doesn't mutate the original.
- `arr.filter(fn)` — returns a **new** array containing only items where `fn` returns truthy. Doesn't mutate the original.
- `arr.sort(fn)` — sorts **in place** (mutates the original array) and also returns it. Without a comparator, elements are sorted as strings (so `[1, 2, 15].sort()` gives `[1, 15, 2]`, not numeric order). Pass a comparator `(a, b) => a - b` for numeric ascending sort.
- `arr.reduce(fn, initial)` — collapses the array into a single value. `fn(accumulator, item)` runs for each item; the accumulator starts as `initial` and carries forward the previous return value. Always pass `initial` — omitting it makes `reduce` error on an empty array.
- Returning an object literal from an arrow function needs extra parens: `arr.map(x => ({ key: x }))` — otherwise `{` is parsed as a function body, not an object.

## Objects for Organizing Code

- **Objects as data structure**: grouping related values under one object (e.g. `{ name, marker }`) beats separate variables — enables namespacing (`playerOne.name` vs a lone `name`) and lets you pass a whole related bundle of data as a single function argument instead of many.
- **Objects as design pattern (OOP)**: objects can hold behavior too, via **methods** (functions stored as object properties), not just data.
- Method shorthand: `getSummary() { ... }` inside an object literal is equivalent to `getSummary: function() { ... }`.
- `this` inside a regular method refers to the object the method was called on — lets a method read/modify that object's own properties (`this.priceUSD *= multiplier`).
- **Arrow functions do not get their own `this`** — using an arrow function for an object method breaks the expected `this` binding, so use regular function syntax (or shorthand) for methods that need `this`.
- **Underscore convention** (`_someProperty`): a naming convention signaling "treat as private/internal," not actual enforced privacy — JS object literals have no real private properties; the property is still fully accessible from outside.

## Arrays

- Declared with `[]` (preferred) or `new Array()` (rarely used — passing a single number to `new Array(n)` creates `n` empty slots, not an array containing that number).
- `arr.at(-1)` gets the last element (steps back from the end for negative indices); cleaner than `arr[arr.length - 1]`. `at(i)` behaves like `arr[i]` for `i >= 0`.
- **Stack/queue methods:**
  - `push(...items)` — adds to the end.
  - `pop()` — removes and returns the last element.
  - `unshift(...items)` — adds to the beginning.
  - `shift()` — removes and returns the first element.
- **Performance:** `push`/`pop` are fast (no renumbering needed — they only touch the end). `shift`/`unshift` are slow — every other element has to be renumbered/shifted in memory.
- Arrays are a special kind of **object** — `arr[0]` is really `arr["0"]` under the hood. This means arrays are copied by **reference**, same as any object (see the primitives-vs-objects note above).
- Looping: `for (let item of arr)` is the standard modern way. Avoid `for...in` on arrays — it iterates all enumerable properties (not just numeric indices) and is much slower; it's meant for generic objects, not arrays.
- `length` isn't a literal element count — it's the highest numeric index + 1, and it's writable: setting `arr.length = 2` truncates the array (irreversibly drops the removed elements).
- Never compare arrays with `==` or `<`/`>` — they compare by reference, not contents. `[] == []` is `false`. Compare item-by-item in a loop instead.
- Misusing an array as a generic object (adding non-numeric properties, leaving "holes" in indices, filling in reverse order) disables the engine's array-specific speed optimizations.

## More Array Methods

- **`delete arr[i]` leaves a hole** — it removes the value but doesn't shrink `length` or shift later elements. Almost never what you want; use `splice` instead.
- `arr.splice(start, deleteCount, ...items)` — the "do anything" method: removes `deleteCount` elements starting at `start` and inserts `items` in their place. Returns the array of removed elements. Mutates in place. `deleteCount = 0` inserts without removing.
- `arr.slice(start, end)` — returns a **new** array copying elements from `start` up to (not including) `end`. Doesn't mutate. `arr.slice()` with no args = a full shallow copy (handy before sorting/mutating when you need to keep the original).
- `arr.concat(...items)` — returns a **new** array; if an argument is itself an array, its elements are spread in rather than nested.
- `arr.forEach(fn)` — runs `fn(item, index, array)` for each element, for side effects only; any return value is discarded (unlike `map`, which collects results).
- **Searching:** `indexOf(item)` / `lastIndexOf(item)` (strict `===`, returns index or `-1`) and `includes(item)` (returns bool). `includes` correctly handles `NaN`; `indexOf` does not (`[NaN].indexOf(NaN)` is `-1`).
- `arr.find(fn)` — returns the **first** matching element (or `undefined`). `findIndex(fn)` returns its index instead (`-1` if none). `findLastIndex(fn)` searches right-to-left.
- `arr.reverse()` — reverses **in place** and also returns the array.
- `str.split(delim)` — string → array. `arr.join(glue)` — array → string (the inverse).
- `Array.isArray(value)` — `typeof` can't tell an array from a plain object (`typeof [] === "object"`); use this instead.
- **`thisArg`** — `find`/`filter`/`map`/etc. all accept an optional second argument that becomes `this` inside the callback (useful when passing an unbound object method as the callback). Rarely needed since an arrow function can capture `this` on its own.
- `arr.some(fn)` / `arr.every(fn)` — return `true` if *any*/*all* elements pass `fn`; short-circuit like `||`/`&&`.
- Less common: `fill(value, start, end)` fills a range with a repeated value; `copyWithin(target, start, end)` copies part of the array over another part of itself; `flat(depth)`/`flatMap(fn)` flatten nested arrays.
- **Sort comparator detail:** it only needs to return positive/negative/zero (not strictly ±1), so `(a, b) => a - b` works fine. For sorting strings correctly (accented letters etc.), prefer `(a, b) => a.localeCompare(b)` over `>`/`<`, since default comparison is by character code and can mis-sort things like `Ö`.
- Only `sort`, `reverse`, and `splice` mutate the array in place; the rest (`map`, `filter`, `slice`, `concat`, etc.) return new arrays.

## Array reference odds & ends (MDN)

- `Array.from(iterableOrArrayLike)` — builds a real array from something array-like (e.g. `arguments`, a `NodeList`) or an iterable.
- `Array.of(...items)` — builds an array from the given arguments, regardless of count/type (avoids the `new Array(2)` "empty slots" trap).
- **Non-mutating versions** of the classic mutators now exist: `toReversed()`, `toSorted()`, `toSpliced()`, `with(index, value)` — same result as `reverse`/`sort`/`splice`/index-assignment, but return a **new** array instead of mutating.
- **Copying an array (shallow):** spread `[...arr]`, `Array.from(arr)`, and `arr.slice()` are all equivalent — they copy the array itself but object *elements* inside are still shared by reference.
- **Deep copy:** use `structuredClone(arr)` (preferred) or `JSON.parse(JSON.stringify(arr))` (older trick — drops functions/`undefined`/etc.) when nested objects also need to be independent.
- Assigning an array to a new variable (`const b = a`) does **not** copy anything — `b` and `a` are two names for the same array (this is just the reference-copy rule for objects again).
- Array methods are **generic**: they only rely on `length` + numeric indices, so they also work on "array-like" objects that aren't real arrays (`arguments`, DOM `NodeList`) via `Array.prototype.method.call(arrayLikeThing, ...)`.

## DOM Manipulation

- The **DOM** is a tree of "nodes" representing the page; **elements** are the node type you'll manipulate most.
- **Selecting:**
  - `document.querySelector(selector)` — first match (CSS-style selector string).
  - `document.querySelectorAll(selector)` — **NodeList** of all matches — looks/acts array-like but is missing many array methods (no `map`, `filter`, etc.). Convert with `Array.from(nodeList)` or `[...nodeList]` if you need those. `forEach` does work directly on a NodeList though.
  - Relational properties also work off an existing reference: `.firstElementChild`, `.lastElementChild`, `.previousElementSibling`, `.nextElementSibling`.
- **Creating/inserting elements:**
  - `document.createElement(tagName)` — creates an element **in memory only**; it's not in the DOM until you insert it.
  - `parentNode.appendChild(childNode)` — appends as last child.
  - `parentNode.insertBefore(newNode, referenceNode)` — inserts before a specific existing child.
  - `parentNode.removeChild(child)` — removes and returns the removed node.
- **Altering elements:**
  - Inline style: `div.style.color = "blue"` (camelCase for kebab-case CSS props — `backgroundColor`, not `background-color`, when using dot notation; bracket notation `div.style["background-color"]` accepts either).
  - Attributes: `setAttribute(name, value)`, `getAttribute(name)`, `removeAttribute(name)`.
  - Classes: `classList.add(cls)`, `classList.remove(cls)`, `classList.toggle(cls)` — toggling classes is generally cleaner than manually setting inline styles.
  - Text: `div.textContent = "..."` — **preferred** over `div.innerHTML = "..."`, since `innerHTML` parses/renders raw HTML and is a common XSS (cross-site scripting) vector if the content ever comes from user input.
- **Script timing:** if your `<script>` tag is in `<head>` and runs before the DOM is parsed, `document.querySelector` etc. won't find anything yet (nodes don't exist yet). Fix: put the script tag at the bottom of `<body>`, or use `<script src="..." defer></script>` in `<head>` (runs after HTML parsing completes).

## Events

- **Three ways to attach event handling**, in increasing order of preference:
  1. Inline HTML attribute: `<button onclick="alert('hi')">` — mixes JS into HTML, and only one handler per element.
  2. JS property: `btn.onclick = () => {...}` — keeps JS separate, but still only one handler per event type per element (assigning again overwrites the previous one).
  3. `btn.addEventListener("click", fn)` — **preferred**: keeps separation of concerns, and supports multiple listeners on the same event without overwriting each other.
- A **callback** is just a function passed as an argument to another function — the handler function passed to `addEventListener` is a callback.
- The listener callback receives an **event object** (conventionally named `e`) — gives access to details like `e.target` (the actual node that triggered the event), which key/button was involved, etc. This is passed automatically by the browser; `e` is just a variable name, not special syntax.
- **Attaching listeners to many nodes at once:** get a NodeList via `querySelectorAll`, then `nodeList.forEach(node => node.addEventListener(...))` — NodeLists support `forEach` even though they lack most other array methods.
- Passing a **named function** as the handler (`btn.addEventListener("click", alertFunction)`) instead of an inline anonymous function keeps code reusable and readable when the same behavior is needed in multiple places.

## Arrow Functions — Basics

- `(arg1, arg2) => expression` — shorthand for a function that evaluates `expression` and implicitly returns it (no `return` keyword needed).
- Single parameter: parens are optional — `n => n * 2` works same as `(n) => n * 2`.
- Zero parameters: parens are required — `() => alert("Hello!")`.
- **Multiline body:** wrapping the body in `{ }` switches to a block body — once you use curly braces, you need an explicit `return`, same as a regular function. `(a, b) => { return a + b; }` — implicit return only applies to the bare-expression form.
- This is the same rule behind the earlier "wrap object literal returns in parens" note — `x => ({ key: x })` needs parens specifically because `{` right after `=>` is parsed as the start of a block body, not an object literal.
- Good for short one-liners and callbacks; can be assigned conditionally too, e.g. `const fn = cond ? () => a() : () => b();`.

## Events — additions

- `element.removeEventListener("click", handlerFn)` — removes a previously added listener. Only works if you pass the **same named function reference** used in `addEventListener` (an inline anonymous function can't be removed this way, since you have no reference to it afterward).
- **Why `addEventListener` is preferred over the `onclick` property specifically:** calling `addEventListener` multiple times on the same event lets you stack multiple independent handlers; assigning `el.onclick = fn` a second time **overwrites** the first, since it's just a property assignment.
- Some event objects carry extra properties specific to their event type — e.g. a `keydown` event's object is a `KeyboardEvent` with a `.key` property telling you exactly which key was pressed (`event.key`).
- `event.preventDefault()` — stops the browser's default action for that event (e.g. stops a form's `submit` event from actually submitting/reloading the page) — used for things like custom client-side validation before allowing submission.

## Event Flow: Bubbling & Capturing

- **Bubbling** — an event starts at the specific element clicked and flows **upward** through its ancestors (button → div → body → html → document). This is the default/most common model.
- **Capturing** — the reverse: starts at `document` and flows **downward** toward the target element.
- **DOM Level 2 event flow** has three phases in order: **capturing** (top → target), **target** (fires on the clicked element itself), **bubbling** (target → top back up). By default, `addEventListener` listens during the bubbling phase; pass a third argument `true` to listen during capturing instead.
- `event.stopPropagation()` — stops the event from continuing to bubble/capture further up or down the tree. Does **not** cancel the browser's default behavior (that's `preventDefault()`'s job — the two are independent).
- `event.preventDefault()` — cancels the default browser action, but does **not** stop the event from continuing to bubble.
- **Other event object properties:** `target` (element event occurred on) vs `currentTarget` (element the *current* handler is attached to — differs from `target` when the event is being handled during bubbling on an ancestor); `type` (event name, e.g. `"click"`); `bubbles`/`cancelable` (booleans describing the event type itself); `defaultPrevented` (true once `preventDefault()` was called).
- The event object only exists for the duration of the handler(s) running — it's discarded afterward.

## Mouse Events

- A single `click` is actually a sequence of **three** events firing in order: `mousedown` → `mouseup` → `click`. If you press down, drag off the element, and release elsewhere, only `mousedown` fires — `click` never fires, since it requires both down *and* up on the same element.
- `dblclick` fires **after** two full `click` sequences (7 events total: down/up/click twice, then `dblclick`). If you listen for both `click` and `dblclick` on the same element, you can't cleanly tell which the user intended without extra logic — worth avoiding unless you actually need double-click behavior.
- `mouseover`/`mouseout` **bubble** and also fire when the pointer crosses into/out of **child** elements — so hovering over a child re-triggers them on the parent.
- `mouseenter`/`mouseleave` do **not** bubble and only fire for the element itself, not its children — generally what you actually want for hover effects, since it avoids the repeated-firing problem `mouseover`/`mouseout` have with nested elements.
- `mousemove` fires very frequently (many times per second) — expensive if the handler does real work. Best practice: only attach the listener while it's actually needed (e.g. `el.onmousemove = handler` then `el.onmousemove = null` when done), or throttle/debounce it.
- `event.button` identifies which physical button triggered the event: `0` = left/main, `1` = middle/wheel, `2` = right, `3`/`4` = browser back/forward buttons.
- **Modifier keys** during a mouse event are read off the event object as booleans: `e.shiftKey`, `e.ctrlKey`, `e.altKey`, `e.metaKey` (Windows key / Cmd key depending on OS).
- **Coordinates:** `e.screenX`/`e.screenY` are relative to the physical screen; `e.clientX`/`e.clientY` are relative to the browser's viewport (client area) — `clientX`/`clientY` is almost always what you want for positioning things relative to the page.

## Keyboard Events

- Three events: `keydown` (fires on press, repeats while held), `keyup` (fires on release), `keypress` (fires only for character-producing keys like letters/numbers, not arrows/Home/End — also repeats while held). **`keypress` is deprecated** in modern JS/browsers — prefer checking `event.key` inside `keydown` instead of relying on `keypress`.
- **Order for a character key:** `keydown` → `keypress` → `keyup`. Both `keydown` and `keypress` fire *before* the textbox's value updates; `keyup` fires *after*.
- **Order for a non-character key** (arrows, Tab, etc.): only `keydown` → `keyup` — no `keypress`.
- `event.key` — the actual character/value produced (e.g. `"z"`, `"Enter"`, `"ArrowLeft"`).
- `event.code` — the **physical** key on the keyboard regardless of layout/shift state (e.g. `"KeyZ"`). Use `code` when you care about physical key position (e.g. WASD game controls); use `key` when you care about the actual character typed.

## Event Delegation

- **The problem:** attaching a separate event listener to every individual child element (e.g. every `<a>` in a menu) doesn't scale — each handler is a function object taking up memory, and setting up many of them adds startup delay.
- **The fix:** attach **one** listener to a common **parent** element instead, and rely on bubbling to catch clicks from any of its children. Inside the handler, check `event.target` to figure out which specific child actually triggered it (e.g. `switch (event.target.id) { ... }`).
- This works precisely because of bubbling covered earlier — a click on a child element bubbles up through its ancestors, so the parent's single listener still fires.
- **Why it's worth doing:** less memory (one handler instead of N), faster page setup, and it naturally handles elements **added to the DOM later** — a listener on individual children would need to be re-attached to any new child, but a delegated listener on the parent covers new children automatically since it's the bubble, not the element itself, being listened for.
- A delegated listener on `document` can also start working immediately once elements render, without waiting for `DOMContentLoaded`/`load`.

## dispatchEvent — Triggering Events Programmatically

- You can generate events from code instead of waiting for real user input: create an event object, then fire it on an element.
  ```javascript
  let clickEvent = new Event("click");
  btn.dispatchEvent(clickEvent);
  ```
  This runs `btn`'s existing `click` listeners exactly as if a real click happened.
- `new Event(type, options)` — `options` is `{ bubbles, cancelable }`, both `false` by default. So a programmatically created event **won't bubble** unless you explicitly pass `{ bubbles: true }`.
- **Prefer specific constructors** (`MouseEvent`, `KeyboardEvent`, `FocusEvent`) over the generic `Event` when simulating a real interaction — they carry event-type-specific data the generic `Event` doesn't, e.g. `new MouseEvent("click", { clientX: 150, clientY: 150, bubbles: true })`.
- `event.isTrusted` — `true` for events from real user action, `false` for anything dispatched via code. Useful if you ever need to distinguish "did a human actually do this" from a simulated event (e.g. anti-abuse checks).

## Custom Events

- Standard events (`click`, `input`, `submit`) are triggered by the browser. **Custom events** are ones you define yourself, for communication between different parts of your own code.
- `new CustomEvent(eventType, { detail: {...} })` — like `Event`, but adds a `detail` property carrying whatever custom data you want to attach to the event.
- Dispatch it the same way as any other event: `element.dispatchEvent(customEvent)`.
- Listeners attach normally via `addEventListener("yourEventName", handler)` — `e.detail` inside the handler holds whatever data you passed in.
- **Why bother:** decouples code that triggers something from code that reacts to it — e.g. a `highlight()` function can fire a `"mark"` event after doing its work, and any number of independent listeners (even in separate files) can react to that, without `highlight()` needing to know or call them directly. This is essentially the pub/sub pattern implemented with native DOM events instead of a custom event-emitter class.

## Callbacks

- A **callback** is simply a function passed into another function as an argument, to be called *by* that function later. That's the entire definition — nothing more magical than that.
- **Where the argument comes from:** when a callback is called with data (`callback(array[i])`), it's the *receiving* function deciding what to pass — the callback's parameter name is just a local placeholder you chose, filled in by whoever calls it. This is exactly why `event` "shows up out of nowhere" inside an event listener: `addEventListener` itself calls your callback and supplies the event object as the argument — you're not creating `event`, you're just naming the parameter that receives it.
- **A named function passed as a callback must NOT be called** in the argument list — pass the bare name (`el.addEventListener('click', myHandler)`), not `myHandler()`. Adding `()` calls it immediately and passes its *return value* as the callback instead of the function itself, which is essentially never what you want.
- `array.forEach`, `array.map`, and `addEventListener` are all just regular functions that happen to accept a callback as one of their arguments — nothing about callbacks is special-cased to these three, it's a general pattern used all over JS (including promises/async code later on).

## Object Constructors

- A **constructor** is just a regular function, called with `new`, used as a template to stamp out multiple similar objects (**instances**) instead of writing each one out as a separate object literal.
  ```javascript
  function Player(name, marker) {
    this.name = name;
    this.marker = marker;
  }
  const player = new Player("steve", "X");
  ```
- **What `new` actually does** (this is the important part — it's doing three things silently): creates a new empty object, sets `this` inside the function to point at that new object, and returns the object automatically — even though the function body has no `return`. Calling `Player("steve", "X")` *without* `new` skips all of this and just runs the function normally (so `this` wouldn't refer to a new object — it'd likely be `undefined` or the global object, a common source of confusing bugs).
- **Safeguard against forgetting `new`:** check `new.target` (a meta-property that's only set when the function was invoked via `new`) and throw if it's missing:
  ```javascript
  function Player(name, marker) {
    if (!new.target) throw Error("You must use 'new' to call this constructor");
    this.name = name;
  }
  ```
- **Prefer `return` over `console.log` inside functions** you're building for reuse (e.g. an `info()` method) — a function that returns a value can be used anywhere (logged, stored, passed on), while one that only logs is locked into that one use.

## The Prototype & Prototypal Inheritance

- Every object in JS has a hidden `[[Prototype]]` — itself just another object — that it inherits properties/methods from. If a property isn't found directly on the object, JS automatically looks up the `[[Prototype]]` chain until it finds it (or reaches the end, where it's `null`, and returns `undefined`).
- **Defining a method "on the prototype"** means attaching it to the constructor's `.prototype` object instead of inside the constructor body — every instance then shares that **one** function instead of each instance getting its own copy (this is the memory-saving fix to the "every player gets its own `sayName` copy" problem from the previous lesson):
  ```javascript
  Player.prototype.sayHello = function() { console.log("Hello!"); };
  player1.sayHello(); // works — found via the prototype chain, not on player1 itself
  ```
- **`Object.getPrototypeOf(obj)`** — reads an object's actual `[[Prototype]]`. **`Constructor.prototype`** — a *different* thing: it's the property on the constructor *function* that determines what new instances' `[[Prototype]]` gets set to when you call it with `new`. Easy to conflate; `.prototype` sets it up front, `Object.getPrototypeOf()` reads it back afterward.
- `.__proto__` is an old, non-standard/deprecated way to get/set `[[Prototype]]` directly on an object — avoid it; use `Object.getPrototypeOf()`/`Object.setPrototypeOf()` instead.
- **The chain keeps going:** `Player.prototype` itself has a `[[Prototype]]`, which by default is `Object.prototype` (where built-ins like `.valueOf()` and `.hasOwnProperty()` actually live). `Object.getPrototypeOf(Object.prototype)` is `null` — the end of every chain. An object can only have **one** `[[Prototype]]` (single inheritance, not multiple).
- **Why bother with prototypes:** (1) memory — one shared function beats N copies; (2) reuse — lets unrelated constructors share behavior via inheritance.
- **Setting up inheritance between constructors** — `Object.setPrototypeOf(Child.prototype, Parent.prototype)` makes `Child` instances inherit `Parent`'s prototype methods too:
  ```javascript
  function Person(name) { this.name = name; }
  Person.prototype.sayName = function() { console.log(`Hi, I'm ${this.name}`); };

  function Player(name, marker) { this.name = name; this.marker = marker; }
  Object.setPrototypeOf(Player.prototype, Person.prototype);

  const p = new Player("steve", "X");
  p.sayName(); // "Hi, I'm steve" — inherited from Person
  ```
  **Must be done *before* creating any instances** — setting it up afterward can cause performance issues.
- **Never do `Player.prototype = Person.prototype`** — this makes both point to the literal **same object in memory**, so editing one (e.g. overwriting a method on `Enemy.prototype` when `Enemy.prototype = Person.prototype`) silently corrupts the other's behavior too. Always use `Object.setPrototypeOf()` instead, which links them without merging them into one object.

## Prototypal Inheritance — additional details

- **Prototype lookup only happens on read.** Writing or deleting a property always acts directly on the object itself — it never reaches up the prototype chain. So assigning `rabbit.walk = function() {...}` doesn't touch `animal`'s `walk` at all; it just adds a new property directly on `rabbit`, which then shadows the inherited one from then on.
- **Exception: getters/setters.** Since a setter is really a function call in disguise, writing to a property that's a setter on the prototype *does* run that setter (defined up the chain) — this is the one case where "writing" isn't purely local to the object.
- **`this` is never affected by where a method is found on the chain** — only by what's before the dot at call time. If `rabbit` inherits a method from `animal`, calling `rabbit.someMethod()` still runs with `this === rabbit`, not `animal`. This is what makes shared prototype methods safe: many objects can share one method, but each one only ever modifies its *own* state through `this`, never the shared prototype's state.
- **⚠️ Common pitfall — shared mutable default values on a prototype.** If a prototype has a property that's a mutable value (like an array or object) rather than a primitive, and an inherited method *mutates* that value in place (e.g. `this.stomach.push(food)`) instead of reassigning it (`this.stomach = [food]`), every inheriting object accidentally shares and corrupts the **same** underlying array/object — because none of them ever created their own copy; they were all just reading (and then mutating) the one found on the prototype. **Fix:** either give each object its own copy of that property directly (not relying on inheritance for it), or always reassign (`=`) instead of mutating in place.
- **`for...in` iterates inherited enumerable properties too** (not just the object's own) — this differs from `Object.keys()`/`Object.values()`, which only return the object's **own** properties and ignore anything inherited. Use `obj.hasOwnProperty(key)` inside a `for...in` loop if you need to filter out inherited properties.
- Built-in methods like `.hasOwnProperty()` don't show up in `for...in` themselves, even though they're technically inherited (from `Object.prototype`) — because they're marked non-enumerable, and `for...in` only lists enumerable properties.

## `this` — The Four Call Patterns

`this` is determined entirely by *how* a function is called. Four patterns:

1. **Simple call** (`someFunction()`, no object before it): in **non-strict mode**, `this` is the global object (`window` in browsers, `global` in Node). In **strict mode** (`"use strict"` at the top of a file/function), `this` is `undefined` instead — strict mode exists partly to stop `this` from silently defaulting to the global object here, which is rarely what you want. Nested functions inherit the strict-mode setting of their outer function.
2. **Method call** (`obj.method()`): `this` is `obj` — the object before the dot at call time (covered already).
3. **Constructor call** (`new Fn()`): `this` is the newly created object (covered already).
4. **Indirect call** — explicitly setting `this` via `call()`, `apply()`, or `bind()` on a function:
   - `fn.call(thisValue, arg1, arg2)` — calls `fn` immediately with `this` set to `thisValue`, args passed individually.
   - `fn.apply(thisValue, [arg1, arg2])` — same as `call`, but args passed as an array.
   - `fn.bind(thisValue)` — does **not** call `fn`; instead returns a **new function** permanently locked to `thisValue`, which you can call later (or pass around, e.g. as a callback) without losing the binding.

**Extracting a method into a variable loses its `this` binding:**
```javascript
const car = { brand: "Honda", getBrand() { return this.brand; } };
const getBrand = car.getBrand;
getBrand(); // undefined — this is no longer `car`, since there's no `car.` at the call site
```
This is exactly the same rule from before (`this` = whatever's before the dot at call time) — there's simply nothing before the dot anymore once it's a bare variable. Fix with `.bind(car)` if you need to pass the method around while keeping its original `this`.

**Arrow functions confirmed again here:** they don't get their own `this` — they use whatever `this` was in the surrounding code where they were *written*, not where they're called. This is why an arrow function used as a constructor's prototype method breaks (it grabs `this` from the outer/global scope instead of the instance), matching what's already noted above about arrow functions and object methods.

## `new` + Return Values (a subtle constructor rule)

- Every function has a `.prototype` object by default — even ordinary, non-constructor-intended functions get one automatically.
- **If a function called with `new` explicitly `return`s something:**
  - Returns a **primitive** (string, number, boolean, etc.) → the return value is **discarded**; `new` returns the auto-created object as normal.
  - Returns an **object** → that returned object is used **instead of** the auto-created one.
- A function that only does `return a + b` (no `this.x = ...` anywhere) still "works" when called with `new` — you get back the auto-created object, and any prototype methods are still reachable through it — but the object itself is empty, since nothing was ever assigned to `this`. Calling an inherited method on it (e.g. one expecting `this.a`/`this.b`) will silently operate on `undefined` values rather than erroring.
- Moral: a real constructor needs to explicitly assign onto `this` (`this.a = a`) — merely `return`ing a computed value from a function called with `new` does not populate the instance.

## `.prototype` vs `[[Prototype]]` — clearing up a common mix-up

- `Constructor.prototype` — a visible property **on the function**, pointing to the prototype (blueprint) object.
- `instance.[[Prototype]]` — a hidden internal link **on the instance**, also pointing to that **same** prototype object — but it does **not** point to the constructor function itself.
- Quick check: `Object.getPrototypeOf(instance) === Constructor` is `false`; `Object.getPrototypeOf(instance) === Constructor.prototype` is `true`.
- Both `.prototype` (from the function) and `[[Prototype]]` (from the instance) are two separate arrows landing on the **same destination** — the shared prototype object — just read with different tools, starting from different places.
- To go from an instance **back to the constructor function**, use `instance.constructor` (works because the prototype object itself holds a `constructor` property pointing back to the function) — a related but distinct link from `[[Prototype]]`.

## Primitive Wrapper Objects (Number, String, Boolean) & Auto-boxing

- `Number`, `String`, `Boolean` are built-in **wrapper constructors** for their respective primitive types.
- **As a plain function call** (no `new`) — does type conversion: `Number("42")` → `42`, `String(5)` → `"5"`. Totally normal, common.
- **As a constructor** (with `new`) — creates a wrapper **object**, not a primitive: `typeof new Number(5)` is `"object"`, not `"number"`. `new Number(5) === 5` is `false` (different types); `new Number(5) == 5` is `true` (loose equality coerces). Generally avoid `new Number()`/`new String()`/`new Boolean()` — write plain primitives (`let n = 5`) instead.
- **Auto-boxing:** primitives have no methods of their own (they're not objects), yet `x.toFixed(2)` still works on a plain number `x`. JS silently, temporarily wraps the primitive in its corresponding wrapper object to perform the lookup/call, then discards the wrapper immediately after — the original primitive is untouched. This is also why `Object.getPrototypeOf(somePrimitive)` returns something (e.g. `Number.prototype`) even though primitives don't really have their own `[[Prototype]]`.
- `.prototype` still only exists on the **constructor function** (`Number.prototype` holds `toFixed`, `toString`, etc., shared via auto-boxing) — never on the primitive value itself; `(5).prototype` is `undefined`, same reasoning as any other value that isn't itself a function.
- Wrapper constructors also carry **static** methods/properties directly on themselves (not on `.prototype`, so not inherited by instances) — e.g. `Number.isInteger()`, `Number.parseFloat()`, `Number.MAX_SAFE_INTEGER`.
