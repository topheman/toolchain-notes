# rolldown / rolldown (js) - bundling

<https://github.com/rolldown/rolldown>

Notes about the [`rolldown`](https://github.com/rolldown/rolldown/tree/main/packages/rolldown) package, which is meant to be consume on the JavaScript side (runtimes like NodeJS).

## Generate bindings

The bindings between rust and `wasm` / `napi` are declared in the [`rolldown_binding`](./explore-shared.md#rolldown_binding) crate.

```sh
cd packages/rolldown
pnpm run build-binding
```

This will generate:

- `packages/rolldown/src/binding.d.ts`: the TypeScript definition corresponding to the `rolldown_binding` crate
- `packages/rolldown/src/binding.js`: the glue code that loads the compiled binary (corresponding to your cpu/arch) and exposes the binded methods
- `packages/rolldown/src/browser.js`
- `packages/rolldown/src/rolldown-binding.{platform}-{arch}.node`: the compiled binary from rust that is executable by node (example on macOS: `rolldown-binding.darwin-x64.node`)
- `packages/rolldown/src/rolldown-binding.wasi-browser.js`: the glue code that loads the wasm version
- `packages/rolldown/src/rolldown-binding.wasi.cjs`: same glue code in common-js

To generate WASI ([WebAssembly System Interface](https://wasi.dev/)) bindings:

```sh
# The compilation target wasm32-wasip1-threads may not be installed
rustup target add wasm32-wasip1-threads
cd packages/rolldown
pnpm run build-binding:wasi:release # or debug
```

<https://doc.rust-lang.org/rustc/platform-support/wasm32-wasip1-threads.html>

This will generate the same files as the previous step and:

- `packages/rolldown/src/rolldown-binding.debug.wasm32-wasi.wasm`
- `packages/rolldown/src/rolldown-binding.wasm32-wasi.wasm`
- `packages/rolldown/src/wasi-worker-browser.mjs`
- `packages/rolldown/src/wasi-worker.mjs`

We now understand where the `*.wasm` files come from.

They are required when `process.env.NAPI_RS_FORCE_WASI` is truthy, which will trigger `require('./rolldown-binding.wasi.cjs')` in [`packages/rolldown/src/binding.js`](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/src/binding.js).

[Difference between napi and napi-rs](../napi/README.md).

## Bundling rolldown with rolldown

A [`packages/rolldown/rolldown.config.mjs`](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/rolldown.config.mjs) file exists which is used by a local previous version of rolldown (aliased as `npm-rolldown`, see in [`packages/rolldown/package.json`](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/package.json)).

That way, rolldown can be bundled with rolldown 🎉.

Most of those files previously generated in `packages/rolldown/src` will be moved to `packages/rolldown/dist/shared` when running `pnpm run build-node` which calls `rolldown` with the config: `node ../../node_modules/npm-rolldown/bin/cli.js -c ./rolldown.config.mjs`.

You have tasks available that will build bindings AND bundle:

- `pnpm run build-native:release` (or debug)
- `pnpm run build-wasi:release` (or debug)

If we look at the the [building section of the contrib-guide](https://rolldown.rs/contrib-guide/building-and-running#building) tells us:

> ### rolldown
>
> To build the rolldown package, there are two commands:
>
> - `just build` / `just build native`
> - `just build native release` (important if running benchmarks)
>
> They will automatically build the Rust crates and the Node.js package. So no matter what changes you made, you can always run these commands to build the latest rolldown package.
>
> ### WASI
>
> Rolldown supports WASI by considering is as a special platform. So we still use the rolldown package to distribute the WASI version of Rolldown.
>
> To build the WASI version, you can run the following command:
>
> - `just build wasi`
> - `just build wasi release` (important if running benchmarks)
>
> Building the WASI version will remove the native version of Rolldown. We designed the local build process on purpose that is you either build the native version or the WASI version. You can't mix them together, though NAPI-RS supports it.

If we look at the [`justfile`](https://github.com/rolldown/rolldown/blob/main/justfile), we can see that everything above gets in place:

```
build target="native" mode="debug":
    pnpm run --filter rolldown build-{{ target }}:{{ mode }}
```

## Use rolldown local version

You can now use the local version you have built (either native or wasi):

```sh
node ./packages/rolldown/bin/cli.js
```

The `rolldown` package is linked to `node_modules` via pnpm workspace automatically, so you can also do:

```sh
pnpm rolldown
```

<https://rolldown.rs/contrib-guide/building-and-running#running>
