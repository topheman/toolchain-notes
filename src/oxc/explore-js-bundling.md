# oxc / Bundling (js)

First, read [napi / Build](../napi/build.md).

You can install the following from npm, which include a pre-built binary - let's talk about how they are bundled.

## Bundling

### oxlint

It doesn't need bundling, the binary is directly used.

> `oxlint` does not require Node.js, the binaries can be downloaded from the [latest GitHub releases](https://github.com/oxc-project/oxc/releases/latest).

<https://www.npmjs.com/package/oxlint>

<https://oxc.rs/docs/guide/usage/linter.html#installation>

### oxc-parser

```sh
cd napi/parser
pnpm build
```

Will generate:

- `napi/parser/index.d.ts`: TypeScript definition of the objects exposed by Rust
- `napi/parser/index.js`: glue code that handles requiring the binary
- `napi/parser/parser.darwin-x64.node` (according to your os/arch)

<https://www.npmjs.com/package/oxc-parser>

<https://oxc.rs/docs/guide/usage/parser.html#installation>

### oxc-transform

```sh
cd napi/transform
pnpm build
```

Will generate:

- `napi/transform/index.d.ts`: TypeScript definition of the objects exposed by Rust
- `napi/transform/index.js`: glue code that handles requiring the binary
- `napi/transform/transform.darwin-x64.node` (according to your os/arch)

<https://www.npmjs.com/package/oxc-transform>

<https://oxc.rs/docs/guide/usage/transformer.html#installation>

## Prepare publishing

Each of the folders in `npm/{oxlint,oxc-parser,oxc-transform}` contain a `scripts/generate-packages.mjs` file that will copy the binaries.
