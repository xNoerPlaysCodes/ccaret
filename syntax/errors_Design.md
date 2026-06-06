# C^ Error Reporting Design

## Format

```
C^:error .. expected `i32`, found `string` ·········· src/main.cc:10:9
10 | i32 x = "hello";
   |         ^^^^^^^ try: change type to `string`

C^:warning .. unused variable `temp` ················ src/main.cc:15:5
15 | i32 temp = compute();
   |     ^^^^ tip: prefix with `_temp`

C^:error .. cannot assign to immutable `x` ·········· src/main.cc:22:1
22 | x = 5;
   | ^ add `mut`: `x mut`
```

## Structure

| Element | Purpose |
|---------|---------|
| `C^:error` / `C^:warning` | Compiler identity + severity. Error in red, warning in yellow. |
| `..` | C^ range operator used as visual separator — unique to our language. |
| Message | Human-readable description. Direct, one line. |
| `src/main.cc:10:9` | Location: file:line:col. Right-aligned to column 52. |
| `··` (filler) | Dim middle dot leader visually bridges message → location. Width adjusts to maintain right-alignment. |
| `10 | code` | Source line with line number. Dim numbers, normal code. |
| `   | ^^^^^^^` | Points to exact column(s). Bold colored. |
| `try:` / `tip:` | Actionable suggestion on the same line as `^`. No extra lines. |

## Color Scheme

| Token | Color |
|-------|-------|
| `C^:error` | Bold Red |
| `C^:warning` | Bold Yellow |
| `..` | Dim |
| `··` (filler) | Dim — leader dots, same tone as `..` |
| Message | Default / Bright White |
| `file:line:col` | Cyan |
| Line numbers | Dim Gray |
| `\|` | Dim Gray |
| `^` / `^^^^` | Bold Red (error) / Bold Yellow (warning), length matches the token |
| `try:` | Green |
| `tip:` | Blue |

## Design Rules

1. **Message first** — human reads the problem before the location.
2. **One line per hint** — `^ try:` keeps suggestion on the same line, no extra boilerplate.
3. **No brackets, no codes, no `= note`** — zero Rust cargo-cult.
4. **Multiple errors stack cleanly** — same format, repeated as needed.
5. **`..` is ours** — not `-->`, not `::`, not `=>`. It's C^'s own range operator used as separator.
6. **Line numbers shown only when code is displayed** — no floating line numbers.
7. **Source code uses `\|`** — pipe, not special unicode box-drawing.
8. **`try:` for actionable fixes**, **`tip:` for best practices**. Clear distinction.
9. **Column numbers are 1-based** and point to the start of the offending token.
10. **`^` length matches the token width** — `^^^^` for a 4-char identifier, `^^^^^^^` for a 7-char string literal.

## Validity Notes

- `src/main.cc:10:9` — col 9 is `"` of `"hello"`, the offending string literal
- `src/main.cc:15:5` — col 5 is `t` of `temp`, the unused variable
- `src/main.cc:22:1` — col 1 is `x`, the immutable variable being assigned to
