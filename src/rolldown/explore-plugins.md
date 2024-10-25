# rolldown / Builtin Plugins

<https://github.com/rolldown/rolldown>

## rolldown_plugin_transform

- relies on:
  - [`oxc::codegen::{CodeGenerator, CodegenReturn}`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_codegen/src/lib.rs)
  - [`oxc::transformer::{TransformOptions, Transformer}`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_transformer/README.md)

1. figure out what type of source using [`ocx::oxc_span::source_type`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_span/src/source_type/mod.rs)
2. parse the source code with [`rolldown_ecmascript::EcmaCompiler`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_ecmascript/src/ecma_compiler.rs)
3. keep track of the comments (extracted by the parser)
4. Extract symbols and scopes using [`oxc::oxc_semantic::SemanticBuilder`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_semantic/src/builder.rs)
5. Pass the ast `oxc::oxc_codegen::CodeGenerator` that will generate code + sourcemap

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_plugin_transform" title="Source Code of rolldown_plugin_transform">📄</a>
