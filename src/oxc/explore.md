# oxc / Explore crates

<https://github.com/oxc-project/oxc>

## oxc_codegen

See more about sourcemaps on [oxc::oxc_sourcemaps](#oxc_sourcemap).

<a href="https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/lib.rs" title="Source Code of oxc_codegen">📄</a>

## oxc_sourcemap

> The sourcemap implement port from [rust-sourcemap](https://github.com/getsentry/rust-sourcemap), but has some different with it.
>
> Encode sourcemap at parallel, including quote sourceContent and encode token to vlq mappings.
> Avoid Sourcemap some methods overhead, like SourceMap::tokens().

The main interface for creating sourcemaps from existing files seems to be [`oxc::oxc_codegen::CodeGen::new().enable_source_map(&filename, &source_text).build()`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/lib.rs) (or any other options allowed by the builder pattern).

### Understand the relation about SourceMap between oxc_codegen and oxc_sourcemap

- [`oxc::oxc_codegen::SourcemapBuilder`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/sourcemap_builder.rs) vs [`oxc::oxc_sourcemap::SourceMapBuilder`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_sourcemap/src/sourcemap_builder.rs) (map vs Map - may be a typo ?)
  - [`oxc::oxc_codegen::Codegen`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/lib.rs) contains a field `sourcemap_builder`: [`Option<oxc::oxc_codegen::SourcemapBuilder>`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/sourcemap_builder.rs)
  - `oxc::oxc_codegen::SourcemapBuilder` contains a field `sourcemap_builder`: [`oxc::oxc_sourcemap::SourceMapBuilder`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_sourcemap/src/sourcemap_builder.rs)
- `oxc::oxc_codegen::SourcemapBuilder` has `into_sourcemap(self) -> oxc::oxc_sourcemap::SourceMap`, which calls:
  - `oxc::oxc_sourcemap::SourceMapBuilder` with `into_sourcemap(self) -> SourceMap`

See more in [`oxc::oxc_codegen`](#oxc_codegen).

<a href="https://github.com/oxc-project/oxc/blob/main/crates/oxc_sourcemap" title="Source Code of oxc_sourcemap">📄</a>

## oxc_span

[`oxc::oxc_span::span::types`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_span/src/span/types.rs)

> A range in text, represented by a zero-indexed start and end offset.

```rust
use oxc_span::Span;
let text = "foo bar baz";
let span = Span::new(4, 7);
assert_eq!(&text[span], "bar");
```

<a href="https://github.com/oxc-project/oxc/blob/main/crates/oxc_span/src/lib.rs" title="Source Code of oxc_span">📄</a>
