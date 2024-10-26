# rolldown / bench

`just setup-bench` is already called with `just setup`.

There are benchmarks for:

- The rust part: [`crates/bench`](https://github.com/rolldown/rolldown/blob/main/crates/bench/Cargo.toml)
  - `just bench-rust`: relies on [`cargo-bench`](https://doc.rust-lang.org/cargo/commands/cargo-bench.html)
  - relies on the utils of [`rolldown_testing`](./explore-shared.md#rolldown_testing)
- The node part: [`packages/bench`](https://github.com/rolldown/rolldown/blob/main/packages/bench/benches/compare.js)
  - `bench-node`: `pnpm --filter bench run bench`
- [`packages/bench/benches/par.js`](https://github.com/rolldown/rolldown/blob/main/packages/bench/benches/par.js)
  - `bench-node-par`: `pnpm --filter bench exec node ./benches/par.js`
