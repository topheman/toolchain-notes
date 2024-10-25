# oxc-resolver / Introduction

<https://github.com/oxc-project/oxc-resolver>

Rust version of [webpack/enhanced-resolve](https://github.com/webpack/enhanced-resolve).

The [tests](https://github.com/oxc-project/oxc-resolver/tree/main/src/tests) are ported from:

- [webpack/enhanced-resolve](https://github.com/webpack/enhanced-resolve/tree/main/test)
- [tsconfig-path](https://github.com/dividab/tsconfig-paths/blob/master/src/__tests__/data/match-path-data.ts) and [parcel-resolver](https://github.com/parcel-bundler/parcel/tree/v2/packages/utils/node-resolver-core/test/fixture/tsconfig) for tsconfig-paths

There is a Node.js API which allows to resolve requests according to the Node.js resolving rules ([esm](https://nodejs.org/api/esm.html#resolution-algorithm) / [cjs](https://nodejs.org/api/modules.html#all-together)).

Those resolving rules are applied, taking in account:

- module type:
  - `esm`: `{ "conditionNames": ["node", "import"] }`
  - `cjs`: `{ "conditionNames": ["node", "import"] }`
- `browserField`: `package.json#browser`
- `mainFields`:
  - `package.json#main`
  - `package.json#module`

And a lot of other things that can alter the resolution algorithm.

This crate is widely used by [`rolldown_resolver`](../rolldown/explore-shared.md#rolldown_resolver).
