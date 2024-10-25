# oxc / Explore crates

<https://github.com/oxc-project/oxc>

## oxc_span

[`oxc::oxc_span::span::types`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_span/src/span/types.rs)

> A range in text, represented by a zero-indexed start and end offset.

```rust
use oxc_span::Span;
let text = "foo bar baz";
let span = Span::new(4, 7);
assert_eq!(&text[span], "bar");
```
