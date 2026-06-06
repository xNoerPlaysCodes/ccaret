# C^ Standard Library Reference

**Rule**: No parens = field load (direct memory). Parens = method call (may compute).

---

## Numeric Types

`i8`, `i16`, `i32`, `i64`, `i128`, `isize` — signed integers.
`u8`, `u16`, `u32`, `u64`, `u128`, `usize` — unsigned integers.
`f32`, `f64` — floating point.

No fields or methods. Pure value types.

---

## Bool, Char, Void

`bool` (`true`/`false`), `char` (32-bit UTF-8), `void` (empty return).

No fields or methods.

---

## String

### `string` — stack-allocated value type
Internally: `struct { u8* data; u64 len; }` — a fat pointer.

| Access | Kind | Returns | Description |
|---|---|---|---|
| `s.data` | field | `u8*` | raw pointer to string bytes |
| `s.len` | field | `u64` | byte length of string |

```c
string name = "Prathmesh";
u64 n = name.len;       // field — no parens
u8* ptr = name.data;    // field — no parens
```

### `hstring` — heap-allocated, owning
For dynamic string growth at runtime.

| Access | Kind | Returns | Description |
|---|---|---|---|
| `hstring.new()` | static | `hstring` | create empty string |
| `hstring.from(s)` | static | `hstring` | create from literal |
| `s.push(str)` | method | void | append string |
| `s.len()` | method | `u64` | byte length |
| `s.clear()` | method | void | remove all content |

```c
hstring mut msg = hstring.new();
msg.push("Hello, ");
msg.push("World!");
u64 n = msg.len();      // method — parens
```

---

## Arrays

### `TYPE[N]` — fixed size, `TYPE[_]` — inferred size
Contiguous memory on stack.

| Access | Kind | Returns | Description |
|---|---|---|---|
| `arr.len` | field | `u64` | element count (compile-time) |
| `arr[i]` | index | `TYPE&` | element reference |

```c
i32[4] arr = [1, 2, 3, 4];
u64 n = arr.len;        // field — no parens
i32 x = arr[0];         // index
```

---

## Vector

### `vec<T>` — dynamic array (heap)

| Access | Kind | Returns | Description |
|---|---|---|---|
| `v.push(val)` | method | void | append element to end |
| `v.pop()` | method | `T` | remove and return last element |
| `v.len()` | method | `u64` | number of elements |
| `v.clear()` | method | void | remove all elements |

```c
vec<i32> mut v = [];
v.push(10);
v.push(20);
i32 last = v.pop();     // 20
u64 n = v.len();        // 1
```

---

## Map

### `map<K, V>` — hashmap

| Access | Kind | Returns | Description |
|---|---|---|---|
| `m.append(k, v)` | method | void | insert key-value pair |
| `m.remove(k)` | method | void | remove entry by key |
| `m.contains(k)` | method | `bool` | check if key exists |
| `m.len()` | method | `u64` | number of entries |
| `m.clear()` | method | void | remove all entries |
| `m[key]` | index | `V&` | read or assign by key |

```c
map<string, i32> mut m = {"one": 1; "two": 2};
i32 x = m["one"];           // index read
m["three"] = 3;             // index assign
m.append("four", 4);        // method insert
bool has = m.contains("one"); // true
```

---

## Set

### `set<T>` — hash set

| Access | Kind | Returns | Description |
|---|---|---|---|
| `s.insert(v)` | method | void | add value |
| `s.remove(v)` | method | void | remove value |
| `s.contains(v)` | method | `bool` | check if value exists |
| `s.len()` | method | `u64` | number of elements |

```c
set<i32> mut s = {1, 2, 3};
s.insert(4);
bool has = s.contains(2);   // true
s.remove(2);
```

---

## Pointers & References

### `T*` — raw pointer, `T&` — safe reference

| Access | Kind | Returns | Description |
|---|---|---|---|
| `ptr.*` | deref | `T` | read/write target value |
| `&var` | address-of | `T*` | get pointer to variable |
| `ref` | auto-deref | `T` | reference uses value directly |

**mut placement:**
- `mut` before `*`/`&` = mutable target
- `mut` after `*`/`&` = mutable binding

```c
i32 mut val = 42;
i32* ptr = &val;         // immutable target, immutable binding
i32 mut* mp = &val;      // mutable target
i32* mut rp = &val;      // mutable binding

i32& ref = val;          // safe ref, auto-deref
i32 mut& mref = val;     // mutable target
```
