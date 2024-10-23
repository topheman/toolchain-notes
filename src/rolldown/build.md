# rolldown / Build

Setup

```sh
just setup && just roll
```

Build the wasi part: needs [wasm32-wasip1-threads](https://doc.rust-lang.org/rustc/platform-support/wasm32-wasip1-threads.html)

```sh
rustup target add wasm32-wasip1-threads # only once
just build wasi
```
