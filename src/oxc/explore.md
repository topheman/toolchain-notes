# oxc / Explore crates

<https://github.com/oxc-project/oxc>

## oxc_ast

[Special section for oxc_ast](./ast/README.md).

### Printing an AST

For this one, you will need to read [`oxc_codegen`](#oxc_codegen) before.

## oxc_codegen

[`oxc:oxc_codegen::Codegen`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/lib.rs) is the struct that holds everything needed to transform an [`oxc::oxc_ast::ast::Program`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_ast/src/ast/js.rs) into a `CodegenReturn` thanks to its `build` method.

The `CodegenReturn` contains:

- `code: String` (the code generated from the ast)
- `map: Option<oxc_sourcemap::SourceMap>` (the sourcemap if activated)

### Codegen::build

- prepares a buffer for the code that will be generated - `self.code.reserve(program.source_text.len())`
- creates a [`HashMap`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/comment.rs) of the comments contained in the AST (if the comments are to be printed - like not in minified code)
- creates a [`oxc::oxc_codegen::SourcemapBuilder`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/sourcemap_builder.rs) (if sourcemaps are active)

Finally, calls the `print` method on the AST, passing itself `&mut Codegen` and a default [`oxc::oxc_codegen::Context`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/context.rs) which will be passed to each `print` calls of each AST node.

### Codegen::gen

Each kind of of AST nodes needs to implement [the following traits (according to their behavior)](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/gen.rs)

```rust
/// Generate source code for an AST node.
pub trait Gen: GetSpan {
    /// Generate code for an AST node.
    fn gen(&self, p: &mut Codegen, ctx: Context);

    /// Generate code for an AST node. Alias for `gen`.
    fn print(&self, p: &mut Codegen, ctx: Context) {
        self.gen(p, ctx);
    }
}
```

```rust
/// Generate source code for an expression.
pub trait GenExpr: GetSpan {
    /// Generate code for an expression, respecting operator precedence.
    fn gen_expr(&self, p: &mut Codegen, precedence: Precedence, ctx: Context);

    /// Generate code for an expression, respecting operator precedence. Alias for `gen_expr`.
    fn print_expr(&self, p: &mut Codegen, precedence: Precedence, ctx: Context) {
        self.gen_expr(p, precedence, ctx);
    }
}
```

See the follow-up in [`oxc_ast`](#oxc_ast).

See more about sourcemaps on [`oxc::oxc_sourcemaps`](#oxc_sourcemap).

<a href="https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/lib.rs" title="Source Code of oxc_codegen">📄</a>

## oxc_sourcemap

> The sourcemap implement port from [`rust-sourcemap`](https://github.com/getsentry/rust-sourcemap), but has some different with it.
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
