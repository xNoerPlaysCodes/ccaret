# syntax/deferred/

This directory holds `.cca` example files for language features that have
been **designed** but are **not implemented** in the current release.

Every file here starts with a `DEFERRED TO vX.Y.Z+` block that explains
when the feature is planned to land and what to use instead in the
current release.

## Why move them here?

The roadmap contract (see `ROADMAP.md` §3.1) lists exactly what ships in
v0.1.0. Anything not on that list is out of scope. Moving the
"designed but not implemented" examples out of `syntax/` makes the
scope visible at a glance: if a feature is in `syntax/`, it's
implemented; if it's in `syntax/deferred/`, it's coming later.

## Files

| File | Feature | Target |
|---|---|---|
| `collections.cca` | Generic `vec<T>`, `map<K,V>`, `set<T>` | v0.2.0 |
| `impl.cca` | `impl` blocks (method dispatch) | v0.2.0 |
| `traits.cca` | Traits + `impl T to Trait` | v0.2.0 |
| `tuples.cca` | Tuples and destructuring | v0.2.0 |

## Rule for moving a file out

When a feature is implemented, move the file back to `syntax/` and
remove the `DEFERRED` header. Update the table above and `ROADMAP.md`
§3.1 in the same PR.
