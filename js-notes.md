# My JS Notes

> Add a line here every time you look something up. First time: look it up. Second time: look it up again. Third time: you shouldn't need to.

## Objects vs Primitives

- Primitives (string, number, boolean, etc.) are copied by **value**. Assigning `let b = a` gives `b` an independent copy — changing `b` never affects `a`.
- Objects (including arrays and functions) are copied by **reference**. `const b = a` makes `b` point to the *same* object as `a` — mutating `b` also changes what `a` sees, because there's only one underlying object.
- This is why DOM manipulation works the way it does: `element.style.backgroundColor = "red"` mutates the actual DOM node because `element` is a reference to it, not a copy.
- Passing to functions follows the same rule: mutating an object parameter inside a function affects the original object outside it; reassigning/incrementing a primitive parameter does not.
- Reassigning a variable (`animal = { species: "cat" }`) breaks its link to whatever it was pointing to before — other variables that referenced the old object are unaffected.

## Accessing Object Properties

- Dot notation: `obj.key` — use when the key name is known and valid as an identifier.
- Bracket notation: `obj["key"]` — required when the key is dynamic (stored in a variable) or not a valid identifier (e.g. has spaces/starts with a number).
