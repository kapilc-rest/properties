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
