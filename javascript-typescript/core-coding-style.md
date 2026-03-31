---
tags: [ conventions, javascript, typescript, coding-style, import, export, jsdoc, errors ]
---

# JavaScript & TypeScript Coding Conventions

Use these conventions every time when project rules are unclear or not enforced.

## Formatting

- Omit curly braces for single-line blocks
- Add an empty line after a block except for the last one (even if it doesn't have curlies)
- Add an empty line before throwing or returning, except when it is the only expression in the block
- Never commit markdown files unformatted unless there is no tool present to format them (prettier, dprint)

## Naming

- Use `camelCase` for variable names and filenames
- Use `PascalCase` for type names and classes
- Use `UPPER_SNAKE_CASE` for constants and environment variables
- Begin function names with a verb (`makeRequest`, `validateInput`)
- Use `kebab-case` for documentation filenames

## TypeScript

- Code for the strictest TypeScript standards even if tsconfig allows for slack
- `type` for aliases, `interface` only for extendable contracts
- Avoid non-null assertions; work around them, or use a utility like
  `isNotNil<T>(input: T | null | undefined): input is T`
- Use type guards instead of `as`

## Functions

- Prefer arrow function expressions (`const fn = () => {}`) over function declarations for
  module-level functions. Use `function` declarations only when hoisting or recursion is required.
- Prefer mild functional programming over object-oriented programming
- Only use classes when inheritance is required
- Avoid static methods in classes
- When a callback extracts a single property, destructure it in the parameter list:
  ```ts
  // GOOD
  levels.map(({ cefr }) => cefr)
  
  // BAD
  levels.map((l) => l.cefr)
  ```
  - **Why:** Clearer signal of intent — the reader immediately sees which property matters without parsing the body.
  - **How to apply:** Any `.map()`, `.filter()`, `.reduce()`, `.flatMap()` etc. where the callback body is a single
  property access. Does not apply when accessing multiple properties or when the variable name adds meaningful context.

## Imports

- Always prefix Node.js inte[core-coding-style.md](core-coding-style.md)rnals with the `node:` prefix
- Relative imports via the shortest path
- Never import packages via relative paths
- Type-only imports with `type` keyword
- Import order: Node.js → External → Internal (if monorepo) → Relative

## Exports

- Named exports
- Export only what's shared internally or by dependents
- Don't expose internal-only exports

## JSDoc Comments

- Document schemas and public APIs include the type definition on `@param`, `@returns`, `@throws`
- Use template to describe type arguments (e.g. `@template T`)
- JSDoc summary lines must use assertive (imperative) voice when the function name begins with a verb.
    - Consistent with the existing codebase style (`createJSONCodec` → "Create a JSON codec",
      `createArrayToRecordCodec` → "Create a bidirectional codec"). Third-person singular ("Creates") reads as a
      description of what the function does; imperative ("Create") reads as a directive, which is the JSDoc convention.
    - Any function whose name starts with a verb (`create`, `get`, `set`, `build`, `parse`, `fetch`, etc.) → JSDoc first
      line is imperative: "Create …", "Get …", "Parse …" — never "Creates …", "Gets …", "Parses …".
- Do not add hyphen before the textual description of a parameter or return value

## Optional Properties in Return Values

- Omit a key entirely rather than including it as `undefined`), use the conditional spread
  pattern: `{ ...base, ...(value !== undefined ? { key: value } : {}) }`
- Prefer this over `{ key: condition ? value : undefined }` — the latter leaves the key present with
  an `undefined` value, which leaks into JSON serialization as `null` or shows up in `Object.keys()`

## Error Handling

- Descriptive errors with `cause`

## Ordering

- Keep declarative lists alphabetically sorted: object keys in config maps, environment variables,
  enum members, union type members, and similar enumerations

## Formatting

- Respect `/.editorconfig`, `/.prettierrc.yml` or `dprint.json` if present
