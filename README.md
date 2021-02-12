# TypeScript
Adding static type definitions to Javascript

## Type guards
```typescript
function isString(s: unknown): s is string {
  return typeof s === 'string';
}

function isNumber(n: unknown): n is number {
  return typeof n === 'number';
}
```

## never
```typescript
function f(): never {
  while (true) {
    if (Math.random() < 0.000001) {
      break;
    }
  }
}
```
```typescript
type error: A function returning 'never' cannot have a reachable end point.
```

## Recursive Types
Encompass all JSON values:
```typescript
type Json =
  | null
  | boolean
  | string
  | number
  | Json[]
  | {[key: string]: Json};
```