# rolldown / rolldown (js)

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
pnpm run build-wasi:release
```

This will generate the same files as the previous step and:

- create `packages/rolldown/src/rolldown-binding.wasm32-wasi.wasm`
  - and move it to `packages/rolldown/dist/shared`
- create `packages/rolldown/src/rolldown-binding.debug.wasm32-wasi.wasm`
  - and move it to `packages/rolldown/dist/shared`
- move any `packages/rolldown/src/rolldown-binding.*` to `packages/rolldown/dist/shared`
- create `packages/rolldown/src/wasi-worker.mjs`
  - and copy it to `packages/rolldown/dist/shared`
- create `packages/rolldown/src/wasi-worker-browser.mjs`
  - and copy it to `packages/rolldown/dist/shared`
- copy any `packages/rolldown/src/rolldown-binding.wasi*` files to `packages/rolldown/dist/shared`
- copy `packages/rolldown/src/binding.{d.ts,js}` to files to `packages/rolldown/dist/shared`

We now have all the blocks to follow the line `process.env.NAPI_RS_FORCE_WASI` which `require('./rolldown-binding.wasi.cjs')` in [`packages/rolldown/src/binding.js`](https://github.com/rolldown/rolldown/blob/main/packages/rolldown/src/binding.js).

<https://doc.rust-lang.org/rustc/platform-support/wasm32-wasip1-threads.html>

[Difference between napi and napi-rs](../napi/README.md).
