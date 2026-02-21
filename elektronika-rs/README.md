# Elektronika RS

This is a modern, cross-platform Rust port of the Elektronika project.

## Architecture

This project uses the following Rust crates to replace the legacy C++ `Alib` framework:

- **Windowing:** `winit` (replacing `Alib/window.cpp`)
- **Graphics:** `wgpu` + `egui` (replacing `Alib/bitmap.cpp`, `DirectX`, `OpenGL`)
- **System:** Standard library (`std::fs`, `std::thread`, `std::net`) replacing custom Win32 wrappers.

## Building

To build the project, you need a working Rust toolchain (Rust 1.75+ recommended).

```bash
cargo build --release
```

## Running

```bash
cargo run --release
```

## Legacy Code References

See `LEGACY_ANALYSIS.md` in the root directory for a detailed breakdown of legacy components and their Rust counterparts.
