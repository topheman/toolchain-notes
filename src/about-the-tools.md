# About the tools

## Vite

[Vite](https://vite.dev/) is a modern build tool for the web. For the moment, it relies on the following bundlers:

- [esbuild](https://esbuild.github.io/) (in go) for development
  - Very fast, serves native esm (no bundling overhead)
- [rollup](https://rollupjs.org/) for production
  - Better plugin api / ecosystem than esbuild

The fact that there are two different bundlers isn't optimal, this is where comes:

## Rolldown

> [Rolldown](https://rolldown.rs/) is a JavaScript bundler written in Rust intended to serve as the future bundler used in Vite. It provides Rollup-compatible APIs and plugin interface, but will be more similar to esbuild in scope.

## Oxc

[Oxc](https://oxc.rs/) (The JavaScript Oxidation Compiler) is a collection of high-performance tools for the JavaScript language written in Rust.

The goal of the project is to provide the next generation toolchain for JavaScript low-level abstraction that each can share so that:

- we share one kind of parser
- we share the same AST
- ...
