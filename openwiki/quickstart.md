---
type: concept
title: Quickstart
description: A quickstart guide mapping the termux-api-package repository.
tags: [quickstart, overview, termux]
verified:
  - by: openwiki/0.9.0
    at: 2026-11-12T04:45:41.100Z
sources:
  - id: openwiki-source-23775c3de52f3ab95a13cb8b
    resource: repo://README.md
generated: { by: "pi", at: "2026-11-12T04:45:41.100Z" }
---

Welcome to `termux-api-package`. This repository contains the client-side command line tools and native helper executable that bridge Termux shell scripts with the `Termux:API` Android application.

## What is this?

The Termux API allows shell scripts to access Android device hardware and system features (like GPS, battery status, camera, and clipboard). This package provides the command-line scripts (e.g. `termux-battery-status`) that users execute. These scripts, in turn, call a native C helper binary to perform the inter-process communication with the Android app.

## Repository Map

- `README.md`: Very brief introduction.
- `termux-api.c`: The core C source code for the helper binary. This file sets up the sockets and manages the AM broadcast intents used to talk to the Termux app.
- `termux-api-broadcast.c`: The main entrypoint for the `termux-api` executable.
- `CMakeLists.txt`: The CMake build configuration used to compile the binary and generate the final wrapper scripts.
- `scripts/`: Directory containing the `.in` templates for all of the API wrapper commands.

## Getting Oriented

To understand how commands are translated and passed to the Android app, begin with the [Architecture Overview](architecture/overview.md).
For the gritty details of the socket connection and intent broadcasting system, read [API Communication](architecture/api-communication.md).
