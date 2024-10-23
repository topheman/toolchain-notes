# napi / Class

## [Class as argument](https://napi.rs/docs/concepts/class#class-as-argument)

- Class is different from Object. Class can have Rust methods and associated functions on it. Every field in Class can mutated in JavaScript.
- So the ownership of the Class is actually transferred to the JavaScript side while you are creating it. It is managed by the JavaScript GC, and you can only pass it back by passing its reference.

## [Custom Finalize logic](https://napi.rs/docs/concepts/class#custom-finalize-logic)

NAPI-RS will drop the Rust struct wrapped in the JavaScript object when the JavaScript object is garbage collected. You can also specify a custom finalize logic for the Rust struct.

[env.adjust_external_memory](https://nodejs.org/api/n-api.html#napi_adjust_external_memory)

> This function gives V8 an indication of the amount of externally allocated memory that is kept alive by JavaScript objects (i.e. a JavaScript object that points to its own memory allocated by a native addon). Registering externally allocated memory will trigger global garbage collections more often than it would otherwise.

## [Arguments types](https://napi.rs/docs/concepts/function)

## [ThreadsafeFunction](https://napi.rs/docs/concepts/threadsafe-function)

https://github.com/rolldown/rolldown/blob/main/crates/rolldown_binding/src/types/js_callback.rs

Concept: https://napi.rs/docs/compat-mode/concepts/thread-safe-function

> JavaScript functions can normally only be called from a native addon's main thread. If an addon creates additional threads, then N-API functions that require a Env, JsValue, or Ref must not be called from those threads.
> When an addon has additional threads and JavaScript functions need to be invoked based on the processing completed by those threads, those threads must communicate with the addon's main thread so that the main thread can invoke the JavaScript function on their behalf. The thread-safe function APIs provide an easy way to do this.
