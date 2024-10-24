# Toolchain Notes

This repository contains the notes I've taken while diving into the source code of [rolldown](https://github.com/rolldown/rolldown) and [oxc](https://github.com/oxc-project/oxc).

My goal is to keep track of my findings and understandings. By open-sourcing this, it might help others (those repositories can be hard to dive into).

Disclaimer: This work is based on myself reading the source code over multiple repositories.

- I might get some things wrong
- Some of my findings may end up out of date somehow (I try not to link to too specific implementations that could change over time)

If you like this work, if you feel you can share improvements, you can contribute.

An html version is available at <https://topheman.github.io/toolchain-notes/>

[You can also read the book directly on github](./src/SUMMARY.md).

## Contributing

Click on the link on top right of the book, this will prompt you to fork the project and you will be able to make the modification directly in the github UI.

You can also propose a PR by hand.

## Generating the html version with mdbook

The html version can be generated with [`cargo-mdbook`](https://crates.io/crates/mdbook).

# Install

```sh
cargo install mdbook
```

# Generate

```sh
mdbook build .
# generated in ./book
```

# Watch mode

```sh
mdbook watch -o .
```
