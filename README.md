# Toolchain Notes

*by Christophe Rosset*

My original goal was to better understand the internals of the oxc ecosystem, which as of this day may well be on track to be the new standard for JavaScript tooling.

This involves subjects that I really enjoy, some of them I know well, some others I wish to get better at. This repository contains the notes I've taken while diving into the source code of [rolldown](https://github.com/rolldown/rolldown), [oxc](https://github.com/oxc-project/oxc) and its ecosystem.

The goal isn't to explain each line of code (they will eventually evolve over time), but to understand how the blocks fit together and how the technologies are used.

If you read this book, you will:

- Get into the internals of [rolldown](./rolldown/README.md), [oxc](./oxc/README.md) (the oxc part is still wip)
- Discover how technologies such as Wasm and Napi are used inside those projects

Disclaimer: This work is based on myself reading the source code over multiple repositories.

- I might get some things wrong
- Some of my findings may end up out of date somehow

An html version is available at <https://topheman.github.io/toolchain-notes/>

[You can also read the book directly on github](./src/SUMMARY.md).

## Contributing

I wrote something incorrect ? You can make a contribution and fix it.

Click on the link on top right of the book, this will prompt you to fork the project and you will be able to make the modification directly in the github UI.

You can also propose a PR by hand.

## Generating the html version with mdbook

The html version can be generated with [`cargo-mdbook`](https://crates.io/crates/mdbook).

### Install

```sh
cargo install mdbook
```

### Generate

```sh
mdbook build .
# generated in ./book
```

### Watch mode

```sh
mdbook watch -o .
```
