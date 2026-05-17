---
name: ts-essentials
description: "You MUST use this skill whenever you write, modify, or review TypeScript code — universal rules for type safety, type modeling, inference, immutability, nullability, and async. Applies to any TypeScript file regardless of project context."
---

# TypeScript Essentials

Universal non-negotiable rules for any TypeScript code. No project-specific context required.

## 1. Absolute prohibitions

| Prohibition               | Use instead                                    |
| ------------------------- | ---------------------------------------------- |
| `any`                     | `unknown` + narrowing or schema validation     |
| `as` type assertion       | Fix the type model or validate at the boundary |
| Non-null assertion `!`    | Narrow with `if` or validate                   |
| `enum`                    | Union literals or `as const` object            |
| `export *` / barrel files | Explicit named imports                         |

```typescript
// never
function process(input: any) { ... }
const id = value as UserId;
const first = arr[0]!;

// always
function process(input: unknown) {
  const parsed = Schema.parse(input);
  ...
}
```

## 2. Type modeling

**Always `type`**. Only `interface` when intentional declaration merging with a library type is required.

Reasons: no silent merging, consistent syntax across objects, unions, tuples, and mapped types.

```typescript
type Invoice = { id: string; amount: number };
type Role = "admin" | "member";
type Result<T> = { ok: true; data: T } | { ok: false; error: string };
```

Use **discriminated unions** for entities with multiple states. Never use optional fields that depend on each other:

```typescript
// never
type Result = { success: boolean; data?: T; error?: string };

// always
type Result<T> = { ok: true; data: T } | { ok: false; error: string };
```

Use union literals or `as const` instead of `enum`:

```typescript
const STATUS = { pending: "pending", done: "done", failed: "failed" } as const;
type Status = (typeof STATUS)[keyof typeof STATUS];
```

## 3. Inference — let TypeScript work

Avoid redundant type annotations where TypeScript can infer:

```typescript
// redundant
const name: string = "Alice";
const ids: string[] = ["a", "b"];

// let it infer
const name = "Alice";
const ids = ["a", "b"];
```

Annotate explicitly at:

- Public function return types (documents the contract)
- Function parameters
- Variables initialized to `null` or `undefined`

## 4. Readonly

Mark object properties `readonly` when they must not be reassigned after construction.
Use `readonly T[]` for arrays that must not be mutated.
Use `as const` for literal objects and arrays that act as constants.

```typescript
type Config = { readonly host: string; readonly port: number };

function process(items: readonly string[]): void { ... }

const DIRECTIONS = ["north", "south", "east", "west"] as const;
type Direction = typeof DIRECTIONS[number];
```

## 5. Nullability

Always narrow before accessing a potentially absent value. Never assume a value exists.

```typescript
// never
const name = user.profile.name; // throws if profile is absent

// always
if (!user.profile) return;
const name = user.profile.name;
```

Use `?.` for reads, `??` for defaults. Do not use `||` for defaults — it swallows `0` and `""`.

## 6. Async

Consistent `async/await`. No floating promises — every promise must be `await`ed, `return`ed, or explicitly handled:

```typescript
// never — floating promise
sendMetric(event);

// always
await sendMetric(event);

// fire-and-forget: explicit and intentional
void sendMetric(event).catch((error: unknown) => {
  logger.error({ error }, "metric failed");
});
```

Do not mix `.then()` with `await` unless a library forces it.

## 7. Tests

Priorities when writing or modifying tests:

- Narrowing and validation (type guards, schema parsing)
- Error branches (throwing, mapping, propagating)
- Async behavior (order, failures, timeouts)
- Edge cases: `null`, `undefined`, empty arrays, invalid input

Do not write tests that only verify TypeScript types — the compiler does that.
