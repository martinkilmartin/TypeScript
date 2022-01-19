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

### Classes

```typescript
class Employee {
  // public by default
  name: string;
  // private
  #vaccinated: boolean;
  
  constructor(name: string, vaccinated: boolean) {
    this.name = name;
    this.#vaccinated = vaccinated;
  }
  
  needsVaccination(): boolean {
    return !this.#vaccinated;
  }
}

new Employee('Alice', true).name;
✅ 'Alice'
new Employee('Alice', true).#vaccinated;
❌ type error: Property '#vaccinated' is not accessible outside class 'Employee' because it has a private identifier.
```

```typescript
class Employee {
  name: string;
  vaccinated: boolean;
  
  constructor(name: string, vaccinated: boolean) {
    this.name = name;
    this.vaccinated = vaccinated;
  }
  
  needsVaccination(): boolean {
    return !this.vaccinated;
  }
}

class Boss extends Employee {
  needsVaccination(): string {
    return this.vaccinated ? 'no' : 'yes';
  }
}

new Boss('Alice', true);
❌ type error: Property 'needsVaccination' in type 'Boss' is not assignable to the same property in base type 'Employee'.
  ❌ Type '() => string' is not assignable to type '() => boolean'.
    ❌ Type 'string' is not assignable to type 'boolean'.
```

### Function Parameters

#### Optional Parameters

```typescript
function add(x: number, y?: number) {
  return x + (y ?? 1);
}
[add(3, 4), add(3)];
✅ [7, 4]
```

❗ Optional parameters must be declared after required parameters.

```typescript
function add(x?: number, y: number) {
  return (x ?? 1) + y;
}
[add(3, 4), add(3)];
❌ type error: A required parameter cannot follow an optional parameter.
```

#### Default Values

```typescript
function add(x: number, y: number = 1) {
  return x + y;
}
[add(3, 4), add(3)];
✅ [7, 4]
```

```typescript
function add(x: number, y: number = undefined) {
  return x + y;
}
[add(3, 4), add(3)];
❌ type error: Type 'undefined' is not assignable to type 'number'.
```

#### Rest Parameters

```typescript
function add(...numbers: number) {
  let sum = 0;
  for (const n of numbers) {
    sum += n;
  }
  return sum;
}
❌ type error: A rest parameter must be of an array type.
```

```typescript
function add(...numbers: number[]) {
  let sum = 0;
  for (const n of numbers) {
    sum += n;
  }
  return sum;
}
[add(), add(1, 2), add(100, 200, 300)]
✅ [0, 3, 600]
add(1, 2, '3');
❌ type error: Argument of type 'string' is not assignable to parameter of type 'number'.
```

⚜ Alternate array syntax : `number[]` | `Array<number>`

```typescript
function add(...numbers: Array<number>) {
  ...
```

### Function Types

#### Optional Parameters

```typescript
type AddFunction = (x:number, y?: number) => number;

const add: AddFunction = (x, y) => {
  return x + (y ?? 1);
};
[add(3, 4), add(3)];
✅ [7, 4]
```

#### Rest Parameters

```typescript
type AddFunction = (...numbers: number[]) => number;

const add: AddFunction = (...numbers) => {
  let sum = 0;
  for (const n of numbers) {
    sum += n;
  }
  return sum;
}
[add(), add(1, 2), add(100, 200, 300)]
✅ [0, 3, 600]
```

#### Default Parameters

```typescript
type AddFunction = (x: number, y: number = 1) => number;
❌ type error: A parameter initializer is only allowed in a function or constructor implementation.
```

### Indexing Into Tuple and Array Types
#### Tuples

```typescript
type HostAndPort = [string, number];
const host: HostAndPort[0] = 'localhost';
const port: HostAndPort[1] = 1234;
const hostAndPort: HostAndPort = [host, port];
hostAndPort;
✅ ['localhost', 1234]
```

```typescript
type HostAndPort = [string, number];
const host: HostAndPort[2] = undefined;
host;
❌ type error: Tuple type 'HostAndPort' of length '2' has no element at index '2'.
```

#### Arrays

```typescript
type Names = string[];
const userNames: Names = ['alice', 'betty'];
const userName: Names[0] = 'cindy';
username;
✅ 'cindy'
```

```typescript
type Names = string[];
const userNames: Names = ['alice', 'betty'];
const userName: Names[999] = 'cindy';
username;
```

```typescript
✅ 'cindy'
type Names = string[];
const userNames: Names = ['alice', 'betty'];
const userName: Names[number] = 'cindy';
username;
✅ 'cindy'
```
