# napi / Build

The [napi-build](https://crates.io/crates/napi-build) crate is used for the build part.

A `build.rs` file is in the crate/package where you want to generate bindings from `src/lib.rs`.

The `build.rs` will only contain this kind of code:

```rust
extern crate napi_build;

fn main() {
    napi_build::setup();
}
```

Then, you will call something like:

```sh
napi build --platform --release
```

Which will generate:

- the binaries for the targeted platforms
- the bindings:
  - `.d.ts` TypeScript decleration files
  - `.js` glue code to load and bind the binary to JavaScript runtime

When running it from a task in a `package.json`, a configuration can be passed to a [`napi` field](https://napi.rs/docs/cli/napi-config.en#schema)

<https://napi.rs/docs/introduction/simple-package>
