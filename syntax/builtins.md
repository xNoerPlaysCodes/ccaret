# C^ Builtins (`@name`)

Builtins are prefixed with `@` so the compiler can identify them immediately.
They are provided by the compiler itself — no import needed.

---

## Core Type Builtins (Compile-time)

| # | Builtin | Purpose |
|---|---------|---------|
| 1 | `@sizeof(T)` | Size of type in bytes |
| 2 | `@alignof(T)` | Alignment requirement of type |
| 3 | `@typeof(expr)` | Deduce type of expression |
| 4 | `@type_name(T)` | String name of type (for debug) |
| 5 | `@offsetof(T, field)` | Byte offset of struct field |
| 6 | `@has_field(T, "name")` | Does struct have field? |
| 7 | `@implements(T, Trait)` | Does type implement trait? |
| 8 | `@is_same(T, U)` | Are two types identical? |
| 9 | `@is_signed(T)` | Is type signed? |
| 10 | `@is_unsigned(T)` | Is type unsigned? |
| 11 | `@is_integral(T)` | Is type integer? |
| 12 | `@is_floating(T)` | Is type float? |
| 13 | `@is_pointer(T)` | Is type a pointer? |

```c
usize sz = @sizeof(i32);             // 4
usize al = @alignof(i32);            // 4
usize off = @offsetof(Person, age);
bool same = @is_same(i32, u32);      // false
bool ptr  = @is_pointer(i32*);       // true
bool float = @is_floating(f64);      // true
```

---

## Error & Panic Builtins

| # | Builtin | Purpose |
|---|---------|---------|
| 14 | `@static_assert(cond, msg)` | Compile-time assertion |
| 15 | `@compile_error(msg)` | Force compile error with message |
| 16 | `@unreachable()` | Mark code path as unreachable |
| 17 | `@panic(msg)` | Runtime abort with message |

```c
@static_assert(@sizeof(i32) == 4, "i32 must be 4 bytes");

fn divide(i32 a, i32 b) -> i32 {
  if b == 0 { @panic("division by zero"); }
  return a / b;
}

match code {
  1 => handle_ok(),
  2 => handle_err(),
  _ => @unreachable(),
}
```

---

## Memory & Pointer Builtins

| # | Builtin | Purpose |
|---|---------|---------|
| 18 | `@addr_of(var)` | Address of variable (returns `T*`) |
| 19 | `@bit_cast(T, val)` | Reinterpret bits as type `T` |
| 20 | `@ptr_offset(ptr, n)` | Offset pointer by `n` elements |
| 21 | `@ptr_add(ptr, n)` | Pointer + integer |
| 22 | `@ptr_sub(ptr, n)` | Pointer - integer |
| 23 | `@align_up(addr, alignment)` | Align address up to boundary |
| 24 | `@align_down(addr, alignment)` | Align address down to boundary |

```c
i32 val = 42;
i32* p = @addr_of(val);

i32[4] arr = [10, 20, 30, 40];
i32* second = @ptr_offset(@addr_of(arr[0]), 1);

f64 bits = @bit_cast(f64, 0x400921FB54442D18);  // reinterpret u64 as f64

usize aligned = @align_up(addr, 16);
```

---

## Volatile & Atomic Builtins

| # | Builtin | Purpose |
|---|---------|---------|
| 25 | `@volatile_load(ptr)` | Volatile read (MMIO) |
| 26 | `@volatile_store(ptr, val)` | Volatile write (MMIO) |
| 27 | `@atomic_load(ptr)` | Atomic load |
| 28 | `@atomic_store(ptr, val)` | Atomic store |
| 29 | `@atomic_xchg(ptr, val)` | Atomic exchange |
| 30 | `@atomic_cas(ptr, exp, des)` | Atomic compare-and-swap |
| 31 | `@atomic_add(ptr, val)` | Atomic fetch-and-add |
| 32 | `@atomic_sub(ptr, val)` | Atomic fetch-and-sub |
| 33 | `@fence()` | Memory barrier |
| 34 | `@cpu_relax()` | CPU hint (spinloop pause) |

```c
// MMIO register access
u8 reg = @volatile_load((u8*)0x4000_1000);
@volatile_store((u8*)0x4000_1000, 0xFF);

// Atomic shared counter
i32 mut counter = 0;
i32 prev = @atomic_add(@addr_of(counter), 1);

// Spinlock with pause
while @atomic_load(@addr_of(lock)) == 1 {
  @cpu_relax();
}

// Memory ordering
@fence();
```

---

## Debug & Branch Hint Builtins

| # | Builtin | Purpose |
|---|---------|---------|
| 35 | `@file()` | Source file path (string) |
| 36 | `@line()` | Source line number (u32) |
| 37 | `@function()` | Current function name (string) |
| 38 | `@assert(cond, msg)` | Runtime assertion |
| 39 | `@likely(cond)` | Branch prediction: likely true |
| 40 | `@unlikely(cond)` | Branch prediction: likely false |

```c
fn debug_log() {
  print("hit {} at {}:{}", @function(), @file(), @line());
}

fn process(i32 val) {
  @assert(val >= 0, "val must be non-negative");

  if @likely(val > 0) {
    // hot path
  } else {
    // cold path
  }
}
```

---

## Summary

| Category | Count | Builtins |
|----------|-------|----------|
| Core Type | 13 | `@sizeof` .. `@is_pointer` |
| Error & Panic | 4 | `@static_assert` .. `@panic` |
| Memory & Pointer | 7 | `@addr_of` .. `@align_down` |
| Volatile & Atomic | 10 | `@volatile_load` .. `@cpu_relax` |
| Debug & Hints | 6 | `@file` .. `@unlikely` |
| **Total** | **40** | |
