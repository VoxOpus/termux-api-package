---
type: concept
title: Architecture Overview
description: High-level overview of the termux-api package architecture and build system.
tags: [architecture, build, scripts, termux]
verified:
  - by: openwiki/0.9.0
    at: 2026-11-12T04:45:41.100Z
sources:
  - id: openwiki-source-d44494ef3e497fea81240ef8
    resource: repo://CMakeLists.txt
  - id: openwiki-source-b06f6a7815189212fb907507
    resource: repo://scripts/termux-battery-status.in
generated: { by: "pi", at: "2026-11-12T04:45:41.100Z" }
---

The `termux-api-package` serves as the client-side shell interface for the Termux API, allowing shell scripts in Termux to access Android device features (like the camera, battery status, or clipboard) which are provided by the `Termux:API` Android app.

## Components

The package consists of two main parts:

1. **Helper Scripts**: A collection of thin shell scripts (e.g., `termux-battery-status`, `termux-camera-photo`). These are the commands users actually run.
2. **The Helper Binary**: A compiled C executable (`termux-api`) that the scripts call under the hood to handle the IPC communication with the Android app.

## Request Flow

When a user runs a command, the flow is:

1. The user executes a script like `termux-battery-status`.
2. The script validates its arguments and then executes the helper binary, passing the underlying API method name (e.g., `BatteryStatus`) and any extra arguments.
3. The `termux-api` binary negotiates the IPC with the Android application (see [API Communication](api-communication.md)), forwarding standard input to the app and writing the app's response to standard output.

## Build System

The package uses CMake to manage its build process.

- The core logic for `termux-api` is compiled into a library (`libtermux-api.so` and `libtermux-api.a`) from `termux-api.c`.
- An executable named `termux-api-broadcast` is built from `termux-api-broadcast.c` (linking against the static library).
- For backwards compatibility, the build system creates a symlink from `termux-api` to `termux-api-broadcast` in the installation's `libexec` directory.
- The helper scripts are located in the `scripts/` directory with `.in` extensions. CMake replaces placeholder variables (like `@TERMUX_PREFIX@`) and installs them to the system's binary directory.
