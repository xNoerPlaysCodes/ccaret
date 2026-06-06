# C Interoperability

C^ works with C. Directly. No wrappers, no bindings generators, no magic.

## Calling C from C^

### 1. Built-in libc

```c
import std.c;  // printf, malloc, free, strlen, etc. pre-declared + linked

i32 main() {
  c.printf("hello %s\n", name.data);
  void* p = c.malloc(64);
}
```

### 2. Declare custom C functions

```c
extern c usize strlen(u8* str);

extern c {
  i32 printf(u8* format, ...);
  void* malloc(usize size);
  void free(void* ptr);
  void* memcpy(void* dest, void* src, usize n);
}
```

### 3. Import C headers directly

Compiler reads the `.h` at compile time and generates `extern c` declarations automatically.

```c
import c "sdl2/SDL.h";
link "SDL2";

i32 main() {
  c.SDL_Init(c.SDL_INIT_VIDEO);
}
```

### 4. Link libraries

```c
link "m";        // -lm
link "pthread";  // -lpthread
link "z";        // -lz
link "./libfoo.a";  // static
```

---

## Compiling C^ for C

```c
// mylib.cc — compiled to .o, callable from C
export i32 add(i32 a, i32 b) {
  return a + b;
}
```

Generates standard C symbols. No name mangling. Use from C like any other .o.

---

## Type Mapping

| C^ | C |
|---|---|
| `i8` .. `i64` | `int8_t` .. `int64_t` |
| `u8` .. `u64` | `uint8_t` .. `uint64_t` |
| `f32`, `f64` | `float`, `double` |
| `bool` | `_Bool` |
| `usize` | `size_t` |
| `isize` | `ptrdiff_t` |
| `char` | `char32_t` |
| `T*`, `T mut*` | `T*` |
| `u8*` | `char*` |
| `string` | pass `.data` (char*) |

---

## String Interop

```c
string s = "hello";

// C^ → C
c.printf("%s\n", s.data);

// C → C^ (wrap raw pointer + len)
string s = string.from(c_ptr, len);
```

## Rule

`c.` prefix is always required. You always see when you're calling C.
