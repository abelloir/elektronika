# Legacy Analysis & Porting Plan

This document outlines the legacy components of the Elektronika project and the strategy for porting them to a modern, cross-platform Rust architecture.

## Legacy Components (C++ / Alib)

The legacy codebase is built on top of `Alib`, a custom C++ framework wrapping Windows APIs.

### Core Systems (`src/Alib`)

| Component | Legacy Implementation | Proposed Rust Replacement | Notes |
| :--- | :--- | :--- | :--- |
| **Windowing** | `window.cpp`, `display.cpp`, `titleBar.cpp` (Win32 API) | **`winit`** | Standard cross-platform window handling. |
| **Graphics** | `bitmap.cpp`, `oglbitmap.cpp`, `d3d.cpp`, `font.cpp` (GDI/OpenGL/Direct3D) | **`wgpu`** + **`egui`** | `wgpu` for high-performance rendering, `egui` for UI overlay. Replaces custom widget drawing. |
| **Audio** | `avi.cpp` (assumed usage), `soundInput.cpp` (DirectSound/WaveIn) | **`cpal`** or **`rodio`** | `cpal` for low-level audio I/O, `rodio` for playback. |
| **Video** | `avi.cpp`, `movie.cpp` (VFW/DirectShow) | **`ffmpeg-next`** or **`gstreamer`** | Video decoding/encoding is complex. FFmpeg bindings are likely the most robust choice. |
| **Networking** | `ftp.cpp`, `udp.cpp` (Winsock) | **`tokio`** + **`reqwest`** | Async networking. `udp` for control protocols (OSC/MIDI over IP?), `reqwest` for HTTP. |
| **Threading** | `thread.cpp` (Win32 Threads) | **`std::thread`** / **`tokio`** | Rust's standard threading or async runtime. |
| **Filesystem** | `file.cpp` (Win32 File API) | **`std::fs`** / **`tokio::fs`** | Standard cross-platform file I/O. |
| **Registry** | `registry.cpp` (Windows Registry) | **`serde`** + **`toml`/`json`** | Configuration files (cross-platform friendly) instead of Windows Registry. |
| **Serial** | `serial.cpp` (Win32 Comm) | **`serialport`** | Cross-platform serial port access. |

### Application Logic (`src/elektronika`)

| Module | Description | Porting Strategy |
| :--- | :--- | :--- |
| **Main Loop** | `main.cpp` | Reimplement using `winit` event loop. |
| **Effects Engine** | `ef3d-*`, `trans-*`, `v3dout.cpp` | Rewrite as **`wgpu` Compute/Fragment Shaders** or Rust modules. |
| **Plugins** | `plugz.cpp` (DLLs) | develop a **WebAssembly (Wasm)** plugin system or a safe Rust trait-based plugin system (dynamic loading via `libloading` if native needed). |
| **Project/State** | `explorer.cpp`, `preview.cpp` | Use a proper ECS (Entity Component System) like **`bevy_ecs`** or a simple state struct, serialized via `serde`. |

## Modernization Goals

1.  **Cross-Platform**: Remove all dependencies on `<windows.h>`, `Win32`, `DirectX` (legacy), `Registry`.
2.  **Safety**: Leverage Rust's memory safety to prevent common C++ bugs (buffer overflows, use-after-free).
3.  **Performance**: Use `wgpu` for GPU-accelerated rendering of the UI and the visualizer engine.
4.  **UI/UX**: Replace the custom "Alib" bitmap-based UI with `egui` (immediate mode GUI) for a modern, responsive, and themeable interface.

## Next Steps

1.  Initialize `elektronika-rs` crate.
2.  Set up the `winit` window and `wgpu` context.
3.  Implement a basic "Render Loop".
4.  Port the "Project State" data structures (serializing to JSON/TOML).
