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
