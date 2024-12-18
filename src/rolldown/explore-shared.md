# rolldown / Shared Crates

<https://github.com/rolldown/rolldown>

## rolldown_binding

Contains the bindings to `wasm` and `napi` for the objects used on the JavaScript side, thanks to the [`#[napi]`](https://napi.rs) macro.

This code is used in [`packages/rolldown`](./explore-rolldown-js-bundling.md#generate-bindings) to generate bindings.

[Difference between napi and napi-rs](../napi/README.md).

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_binding" title="Source Code of rolldown_binding">📄</a>

## rolldown_common

### rolldown_common::file_emitter

The [`FileEmitter`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_common/src/file_emitter.rs) is instanciated and passed as an `Arc<FileEmitter>` to the [`rolldown_plugin::PluginDriver`](#rolldown_pluginplugindriver)

## rolldown_css

Tiny wrapper around [lightningcss](https://lightningcss.dev)

> An extremely fast CSS parser, transformer, bundler, and minifier (built in rust)

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_css" title="Source Code of rolldown_css">📄</a>

## rolldown_ecmascript

- `oxc::allocator::Allocator` is needed for the parser - <https://github.com/oxc-project/oxc/blob/main/crates/oxc_allocator/src/lib.rs>
  - Oxc uses a bump-based memory arena for faster AST allocations - (example of bump alocator : [bumpalo](https://docs.rs/bumpalo/3.16.0/bumpalo/index.html))

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_ecmascript" title="Source Code of rolldown_ecmascript">📄</a>

## rolldown_fs

Thin abstraction over the traits `oxc_resolver::{FileMetadata, FileSystem}`.

For the implementation, see [oxc-project/oxc-resolver](https://github.com/oxc-project/oxc-resolver), a Rust version of [webpack/enhanced-resolve](https://github.com/webpack/enhanced-resolve).

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_fs" title="Source Code of rolldown_fs">📄</a>

## rolldown_loader_utils

- [`json_to_esm`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_loader_utils/src/json_to_esm.rs)
  - `format!("export const {key} = {};\n", serde_json::to_string_pretty(value)?)`
  - relies on
    - [`oxc_syntax::identifier::is_identifier_name`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_syntax/src/identifier.rs) to make sure the export is a valid identifier (otherwise, creates a temporary `const` and `export as`)
    - [`oxc_syntax::keyword::is_reserved_keyword_or_global_object`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_syntax/src/keyword.rs)
- [`text_to_string_literal`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_loader_utils/src/text_to_esm.rs)
- [`binary_to_esm`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_loader_utils/src/binary_to_esm.rs)
  - Based on the platform, it will be `__toBinary` or `__toBinaryNode`, the implementation is in [`crates/rolldown/src/runtime`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown/src/runtime/index.js)
  - `import { __toBinary } from ./some-module; export default __toBinary(base64encoded)`

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_loader_utils" title="Source Code of rolldown_loader_utils">📄</a>

## rolldown_plugin

- [`trait Plugin`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_plugin/src/plugin.rs) contains default implementation for the interfaces it declares:
  - `build_start`, `resolve_id`, `load`, `transform`, `transform_ast` and many more
- [`trait Pluginable`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_plugin/src/pluginable.rs)
  - exposes interfaces without implementations for `call_load`, `call_transform`, `call_transform_ast`, that kinda stuff
  - should not be used directly, it is recommended to use the `Plugin` trait - comment from source code:
    - "The main reason we don't expose this trait is that it used `async_trait`, which make it rust-analyzer can't provide a good auto-completion experience."
- [`impl<T: Plugin> Pluginable for T`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_plugin/src/pluginable.rs) block creates implementations for the methods `call_*` based on `Plugin` trait implementation

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_plugin" title="Source Code of rolldown_plugin">📄</a>

### rolldown_plugin::PluginDriver

[`PluginDriver`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_plugin/src/plugin_driver/mod.rs)

- instanciated by the [`BundlerBuilder`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown/src/bundler_builder.rs)
  - plugins: `Vec<SharedPluginable>` - list of plugins
  - resolver: `&Arc<Resolver>` - see [`rolldown_resolver`](#rolldown_resolver)
  - file_emitter: `SharedFileEmitter` - see [`rolldown_common::file_emitter` (source)](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_common/src/file_emitter.rs) / [`rolldown_common::file_emitter`](#rolldown_commonfile_emitter)
  - options= `SharedNormalizedBundlerOptions = Arc<NormalizedBundlerOptions>`
  - watch_files
- `PluginDriver::iter_plugin_with_context_by_order(self, [PluginIdx])` is available: it loops over plugins, **making their context available**
  - it is used in many methods such as `PluginDriver::{build_start,resolve_id,load,transform,transform_ast,module_parsed,build_end,watch_change,close_watcher}`
  - those implementation are in the files [`rolldown_plugin::plugin_driver::build_hooks`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_plugin/src/plugin_driver/build_hooks.rs), [`rolldown_plugin::plugin_driver::output_hooks`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_plugin/src/plugin_driver/output_hooks.rs) and [`rolldown_plugin::plugin_driver::watch_hooks`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_plugin/src/plugin_driver/watch_hooks.rs)
- for each plugin, a [`PluginContextImpl`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_plugin/src/plugin_context.rs) (something close to a `Arc<PluginContext>`) is created
  - this list of contexts is pushed to `PluginDriver::contexts`
  - each of the contexts share references (as `Weak` or `Arc`) to what the `PluginDriver` is holding, such as `resolver`, `file_emitter`, `options`, `watch_files` ...

## rolldown_resolver

This plugin relies on the traits `rolldown_fs::{FileSystem, OsFileSystem}` from [`rolldown_fs`](#rolldown_fs) which relies on `oxc_resolver::{FileMetadata, FileSystem}`.

This is the plugin in charge of resolving the paths of the imports, which is a very tricky thing in JavaScript. The resolving part is handled by the [`oxc_resolver`](../oxc-resolver/README.md) crate (in order to be able to share).

1. `rolldown_resolver::Resolver::new` creates an instance of the resolver
  - based on
    - [`ResolveOptions`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_common/src/inner_bundler_options/types/resolve_options.rs) (handles all the default cases)
    - [`Platform`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_common/src/inner_bundler_options/types/platform.rs) which may be `node`, `browser` or `neutral`
  - instanciates `Resolver::{default_resolver,import_resolver,require_resolver}` with [`oxc_resolver::ResolverGeneric<T>`](https://github.com/oxc-project/oxc-resolver/blob/main/src/lib.rs) passing the options resolved above
2. Call site of `rolldown_resolver::Resolver::new` is in `BundlerBuilder`, while creating [`PluginDriver`](#rolldown_pluginplugindriver)
3. `rolldown_resolver::Resolver::resolve` is exposed
  - it accepts:
    - importer: `Option<&Path>` - the path from where the module to be imported is to be resolved
    - specifier: `&str` - the "name" of the module to resolve
    - import_kind: `rolldown_common::ImportKind` whether it is an import, a dynamic import, a require, an `AtImport` (css)
  - it resolve the directory of the importer from `importer`
  - calls the adequate resolver (from `oxc_resolver`) based on `import_kind` with (`importer`, `specifier`)
  - retrieves the `package.json` related to the module being resolved, since it can affect how we should resolve it
  - caches the `package.json`
  - calculates the following for the return:
    - module_type: `rolldown_common::ModuleDefFormat`:
      - ending with `.mjs` or `.cjs` is easy
      - however, the `type` field of the `package.json` may affect the resolution (`module`, `commonjs`)

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_resolver" title="Source Code of rolldown_resolver">📄</a>

## rolldown_rstr

Exposes `rolldown_rstr::Rstr` which is a thin wrapper over [`oxc::span::CompactStr`](https://github.com/oxc-project/oxc/blob/main/crates/oxc_span/src/compact_str.rs), which is a wrapper over the [`compact_str`](https://crates.io/crates/compact_str) crate.

> A memory efficient string type that can store up to 24* bytes on the stack.
>
> A `CompactString` is a more memory efficient string type, that can store smaller strings on the stack, and transparently stores longer strings on the heap (aka a small string optimization). It can mostly be used as a drop in replacement for `String` and are particularly useful in parsing, deserializing, or any other application where you may have smaller strings.

`rolldown_rstr::Rstr` is used in many places in the project.

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_rstr" title="Source Code of rolldown_rstr">📄</a>

## rolldown_sourcemap

Exposes [`collapse_sourcemaps(mut sourcemap_chain: Vec<&SourceMap>) -> SourceMap`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_sourcemap/src/lib.rs).

It collapses multiple sourcemaps generated by calls to `oxc::oxc_codegen::CodeGen::new().enable_source_map(&filename, &source_text).build()` into one giant sourcemap.

Relies on [`oxc::sourcemap::*`](https://github.com/oxc-project/oxc/tree/main/crates/oxc_sourcemap).

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_sourcemap" title="Source Code of rolldown_sourcemap">📄</a>

## rolldown_testing

Utils for used for bench testing. Used for [benchmark](./bench.md).

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_testing" title="Source Code of rolldown_testing">📄</a>

## rolldown_tracing

See [contribution guide chapter about tracing/logging](https://rolldown.rs/contrib-guide/tracing-logging).

This crate exposes [`try_init_tracing`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_tracing/src/lib.rs) which is called when building the bundler and correctly initializes tracing according to env vars.

[`rolldown_binding::bundler::Bundler`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_binding/src/bundler.rs) calls [`try_init_custom_trace_subscriber`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_binding/src/utils/mod.rs) which does the same as [`try_init_tracing`](https://github.com/rolldown/rolldown/blob/main/crates/rolldown_tracing/src/lib.rs) but ensures to call [`napi_env.add_env_cleanup_hook`](https://nodejs.org/api/n-api.html#napi_add_env_cleanup_hook) and manually `flush` and `drop` `tracing_chrome::FlushGuard`.

This crate relies on:

- [`tracing-chrome`](https://crates.io/crates/tracing-chrome)
- [`tracing-subscriber`](https://crates.io/crates/tracing-subscriber)

<a href="https://github.com/rolldown/rolldown/tree/main/crates/rolldown_tracing" title="Source Code of rolldown_tracing">📄</a>
