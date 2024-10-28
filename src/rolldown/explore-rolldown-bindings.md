# rolldown / Bindings (rust/js)

Before reading this chapter, take a look at:

- [`rolldown_binding` crate](./explore-shared.md#rolldown_binding)
- [rolldown / rolldown (js) - bundling](./explore-rolldown-js-bundling.md)

The topic of this chapter is how does the `napi` bindings declared in the [`rolldown_binding`](https://github.com/rolldown/rolldown/tree/main/crates/rolldown_binding) crate (and the [code generated from it](./explore-rolldown-js-bundling.md)) are finally consumed.

## createBundler

[packages/rolldown/src/utils/create-bundler.ts](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/src/utils/create-bundler.ts)

### options

#### normalize

- There are multiple `normalize-*.ts` files exporting different `nomalize*()` functions
- Those functions compose into `normalizeInputOptions` and `normalizeOutputOptions`
- They may accept some zod schemas - examples:
  - [packages/rolldown/src/options/input-options.ts](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/src/options/input-options.ts)
  - [packages/rolldown/src/options/output-options.ts](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/src/options/output-options.ts)

#### bindingify

Once normalized, those options are instanciated with objects following the interfaces [`packages/rolldown/src/binding.d.ts`](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/src/binding.d.ts) which was generated based on [`rolldown_binding`](./explore-rolldown-js-bundling.md#generate-bindings).

- There are multiple `bindingify-*.ts` files exporting different `bindingify*()` functions
- Those functions accept a the normalized TS types and return objects that will be able to interact with napi

### result

It returns a `Bundler` as describe in [`packages/rolldown/src/binding.d.ts`](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/src/binding.d.ts):

```ts
export declare class Bundler {
  constructor(inputOptions: BindingInputOptions, outputOptions: BindingOutputOptions, parallelPluginsRegistry?: ParallelJsPluginRegistry | undefined | null)
  write(): Promise<BindingOutputs>
  generate(): Promise<BindingOutputs>
  scan(): Promise<void>
  close(): Promise<void>
  watch(): Promise<BindingWatcher>
}
```

This means the calls to the methods `write`, `generate`, `scan`, `close`, `watch` on the NodeJS side will be routed via napi to [rolldown_binding::bundler](crates/rolldown_binding/src/bundler.rs). Where we can see the following implementations in rust:

```rust
impl Bundler {
  #[napi(constructor)]
  #[cfg_attr(target_family = "wasm", allow(unused))]
  pub fn new(
    env: Env,
    mut input_options: BindingInputOptions,
    output_options: BindingOutputOptions,
    parallel_plugins_registry: Option<ParallelJsPluginRegistry>,
  ) -> napi::Result<Self> {
    // ...
  }

  #[napi]
  #[tracing::instrument(level = "debug", skip_all)]
  pub async fn write(&self) -> napi::Result<BindingOutputs> {
    self.write_impl().await
  }

  #[napi]
  #[tracing::instrument(level = "debug", skip_all)]
  pub async fn write(&self) -> napi::Result<BindingOutputs> {
    self.write_impl().await
  }

  #[napi]
  #[tracing::instrument(level = "debug", skip_all)]
  pub async fn generate(&self) -> napi::Result<BindingOutputs> {
    self.generate_impl().await
  }

  #[napi]
  #[tracing::instrument(level = "debug", skip_all)]
  pub async fn scan(&self) -> napi::Result<()> {
    self.scan_impl().await
  }

  #[napi]
  #[tracing::instrument(level = "debug", skip_all)]
  pub async fn close(&self) -> napi::Result<()> {
    self.close_impl().await
  }

  #[napi]
  #[tracing::instrument(level = "debug", skip_all)]
  pub async fn watch(&self) -> napi::Result<BindingWatcher> {
    self.watch_impl().await
  }
}
```
