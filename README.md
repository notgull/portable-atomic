# portable-atomic

[![crates.io](https://img.shields.io/crates/v/portable-atomic?style=flat-square&logo=rust)](https://crates.io/crates/portable-atomic)
[![docs.rs](https://img.shields.io/badge/docs.rs-portable--atomic-blue?style=flat-square)](https://docs.rs/portable-atomic)
[![license](https://img.shields.io/badge/license-Apache--2.0_OR_MIT-blue?style=flat-square)](#license)
[![rustc](https://img.shields.io/badge/rustc-1.34+-blue?style=flat-square&logo=rust)](https://www.rust-lang.org)
[![build status](https://img.shields.io/github/workflow/status/taiki-e/portable-atomic/CI/main?style=flat-square&logo=github)](https://github.com/taiki-e/portable-atomic/actions)

Portable atomic types.

- Provide all atomic integer types (`Atomic{I,U}{8,16,32,64}`) for all targets that can use atomic CAS. (i.e., all targets that can use `std`, and most no-std targets)
- Provide atomic load/store for targets where atomic is not available at all in the standard library. (riscv without A-extension, msp430, avr)
- (optional) Provide `Atomic{I,U}128`.
- (optional) Provide `AtomicF{32,64}`.
<!-- - (optional) Provide generic `Atomic<T>` type. -->
- (optional, [single-core only](#optional-cfg)) Provide atomic CAS for targets where atomic CAS is not available in the standard library. (thumbv6m, riscv without A-extension, msp430, avr)

## Optional features

- **`fallback`** *(enabled by default)*<br>
  Enable fallback implementations.

- **`i128`**<br>
  Provide `Atomic{I,U}128`.

  Note:
  - This implicitly enables the `fallback` feature.
  - Atomic operations are only available for Rust 1.59+ on x86_64 and nightly on aarch64, otherwise the fallback implementation is used.
  - On x86_64, when `cmpxchg16b` target feature is not enabled at compile time, this uses the fallback implementation. `cmpxchg16b` is enabled by default only on macOS.

  The above status is likely to change in the future:
  - When `Atomic{I,U}128` is stabilized in the standard library.
  - When 128-bit atomic operations are supported in other architectures. ([riscv](https://lists.riscv.org/g/tech/topic/requirements_for_128_bit/76126473?p=))

  If you need support for dynamic CPU feature detection, use the `i128-dynamic` feature.

- **`i128-dynamic`**<br>
  Similar to the `i128` feature, but tries to use `cmpxchg16b` in more cases based on dynamic CPU feature detection.

  Note:
  - Dynamic detection is only enabled in nightly, otherwise it works the same as the `i128` feature.
  - When `cmpxchg16b` target feature is enabled at compile time, this works exactly the same as the `i128` feature.
  - If both `i128` and `i128-dynamic` features are used in the dependency graph, `i128-dynamic` takes precedence.
  - This is compatible with no-std (as with all features except `std` and `parking_lot`).

- **`float`**<br>
  Provide `AtomicF{32,64}`.
  Note that most of `fetch_*` operations of atomic floats are implemented using CAS loops, which can be slower than equivalent operations of atomic integers.

<!-- TODO
- **`generic`**<br>
  Provides generic `Atomic<T>` type.
-->

- **`std`**<br>
  Use `std`.

- **`serde`**<br>
  Implement `serde::{Serialize,Deserialize}` for atomic types.

  Note:
  - The MSRV when this feature enables depends on the MSRV of [serde].

- **`parking_lot`**<br>
  Use [parking_lot] in global locks of fallback implementation.

  Note:
  - This is not compatible with no-std.
  - The MSRV when this feature enables depends on the MSRV of [parking_lot].

## Optional cfg

- **`--cfg portable_atomic_unsafe_assume_single_core`**<br>
  Assume that the target is single-core.
  When this cfg is enabled, this crate provides atomic CAS for targets where atomic CAS is not available in the standard library.

  Note: This cfg is `unsafe`, and enabling this cfg for multi-core systems is **unsound**.

  This is intentionally not an optional feature. If this is an optional feature, dependencies can implicitly enable the feature, resulting in the use of unsound code without the end-user being aware of it.

[parking_lot]: https://github.com/Amanieu/parking_lot
[serde]: https://github.com/serde-rs/serde

## License

Licensed under either of [Apache License, Version 2.0](LICENSE-APACHE) or
[MIT license](LICENSE-MIT) at your option.

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall
be dual licensed as above, without any additional terms or conditions.
