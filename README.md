# TypeScript
Adding static type definitions to Javascript

## Everyday Typescript

### Types

```typescript
// Types must be unique
type User = { name: string };
type User = { email: string };
❌ type error: Duplicate identifier 'User'

// Intersection
type HasName = { name: string };
type HasEmail = { email: string };
type User = HasName & HasEmail;

const user: User = {
  name: "Sally",
  email: "sally@email.net",
};

user;
✅ {email: 'sally@email.net', name: 'Sally'}
```

### Interfaces

```typescript
// Interfaces with the same name are merged
// Note lack of '=' compared to Types
interface User {
  name: string;
}
interface User {
  email: string;
}

const user: User = {
  name: "Sally",
  email: "sally@email.net",
};

user;
✅ {email: 'sally@email.net', name: 'Sally'}
```

### Interfaces v `type` keyword

1. Interfaces can produce better error messages in some cases
2. Interfaces can sometimes compile faster than object types
3. The official Typescript docs uses interfaces in its examples
4. The Typescript team endorses interfaces "it should be just interfaces for anything they can model"

### Optional properties

```typescript
type User = {
  name: string;
  email: string | undefined;
};
// equivalent:
type User = {
  name: string;
  email?: string;
};
```

### Promises

```typescript
const promise: Promise<number> = Promise.resolve(99);
promise;
✅ {fulfilled: 99}
```

```typescript
const promise: Promise<string> = Promise.resolve(99);
promise;
❌ type error: 'Promise<number>' is not assignable to type 'Promise<string>'.
❌ Type 'number' is not assignable to type 'string'.
```

```typescript
const promise: Promise<string> = Promise.resolve(100).then((n) => n.toString());
promise;
✅ {fulfilled: '100'}
```

### Indexing into object types

```typescript
type ConnectionOptions = {
    host: string
    port: number
    credentials: {
        username: string
        pasword: string
    }
};

function buildConnectionOptions(
    host: string
    port: number
    credentials: ConnectionOptions['credentials']
): ConnectionOptions {
    return {host, port, credentials}
};
```

### `object` type

`object` type is almost never useful

```typescript
const user: object = {
  name: "Sally",
};
user.name;
❌ type error: Property 'name' does not exist on type 'object'
```

### Enums

```typescript
enum HTTPMethods {
    Get = 'GET',
    Post = 'POST',
}

const method: HTTPMethods = HTTPMethods.Post;
method;
✅ 'POST'
```

```typescript
// Defaulting incremental values
enum HTTPMethods { Get, Post };
const get: HTTPMethods = HTTPMethods.Get;
const post: HTTPMethods = HTTPMethods.Post;
get;
✅ 0
post;
✅ 1
```

```typescript
// Defaulting incremental values
enum HTTPMethods { Get = 10, Post };
const get: HTTPMethods = HTTPMethods.Get;
const post: HTTPMethods = HTTPMethods.Post;
get;
✅ 10
post;
✅ 11
```

```typescript
// Enforced use of symbolic names
enum HTTPMethods {
    Get = 'GET',
    Post = 'POST',
};
const get: HTTPMethods = 'GET'
get;
❌ type error: Type '"GET"' is not assignable to type 'HTTPMethods'.
```

❗ Enums add additional additional JavaScript, breaking TypeScript's core "type-level extension" rule.

### Pick & Omit

```typescript
type User = {
    name: string
    email: string
    age: number
};

type NameAndEmail = Pick<User, 'name' | 'email'>;

const sally: NameAndEmail = {name: 'Sally', email: 'sally@email.net'};
sally;
✅ {email: 'sally@email.net', name: 'Sally'}

// equivalent with Omit
type Ageless = Omit<User, 'age'>;

const sarah: Ageless = {name: 'Sarah', email: 'sarah@email.net'};
sarah;
✅ {email: 'sarah@email.net', name: 'Sarah'}
```

### Impossible Conditions

```typescript
let s = 'it works';
if (1 === 2) {
    s = 'never'
};
s;
❌ type error: This condition will always return 'false' since the types '1' and '2' have no overlap.
```

❗ Typescript compares **literal types**, _not_ **values**.

```typescript
let s = 'it works';
if (2 + 2 === 5) {
    s = 'never'
};
s;
✅ 'it works'
```

```typescript
let s = 'it works'
if ('string 1' === 'string 2') {
    s = 'never';
};
s;
❌ type error: This condition will always return 'false' since the types '"string 1"' and '"string 2"' have no overlap.
```

```typescript
let s = 'it works'
if ('' + 'string 1' === 'string 2') {
    s = 'never';
};
s;
✅ 'it works'
```

### `keyof` `typeof`

```tsx
const SIZES = {
  no: "",
  lg: " btn-lg",
  md: " btn-md",
  sm: " btn-sm",
};

type Props = {
  text: string;
  size?: keyof typeof SIZES;
};

const Button = ({ text, size = "no" }: Props): JSX.Element => (
  <button className={SIZES[size]}>{text}</button>
);
```

### Type guards

```typescript
function isString(s: unknown): s is string {
  return typeof s === "string";
}

function isNumber(n: unknown): n is number {
  return typeof n === "number";
}
```

### never

```typescript
function f(): never {
  while (true) {
    if (Math.random() < 0.000001) {
      break;
    }
  }
};
f();
❌ type error: A function returning 'never' cannot have a reachable end point.
```

### Recursive Types

Encompass all JSON values:

```typescript
type Json = null | boolean | string | number | Json[] | { [key: string]: Json };
```
### `as` Is Dangerous!

```typescript
const aNumber: unknown = 5;
const aString: aNumber as string;
aString;
✅ 5
```

```typescript
const aNumber: unknown = 5;
const aString = aNumber as string;
const length: number = aString.length;
length;
✅ undefined
```

```typescript
const anUnknown: unknown = true;
const length: number = (anUnknown as string).length;
length;
✅ undefined
```
