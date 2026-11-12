---
type: concept
title: API Communication
description: How the termux-api helper binary communicates with the Termux:API Android app using abstract namespace sockets and broadcasts.
tags: [architecture, sockets, intents, android]
verified:
  - by: openwiki/0.9.0
    at: 2026-11-12T04:45:41.100Z
sources:
  - id: openwiki-source-c61a34cc0963133cb36452d5
    resource: repo://termux-api.c
generated: { by: "pi", at: "2026-11-12T04:45:41.100Z" }
---

The `termux-api` program serves as a native bridge between shell scripts and the `Termux:API` Android application.

## Socket Relay

When invoked, the main `run_api_command` function generates two unique UUIDs which act as names for Linux abstract namespace sockets (one for input/stdin and one for output/stdout). It then spawns two pthreads:

- One thread reads from standard input and forwards the data into the input socket.
- Another thread reads from the output socket and writes it to standard output.

These UUID socket addresses are passed to the Termux API application, which connects to them to receive input parameters (like JSON data for notifications) and send back command results.

## Contacting the Plugin

The helper tries two methods to wake the Termux:API app and pass the command arguments and socket addresses:

1. **Direct Socket Connection**: It attempts to connect to an abstract UNIX socket named `com.termux.api://listen`. If the Termux:API app is already running and listening, this is faster. The helper sends the command arguments, socket UUIDs, caller UID, PID, and start time as a JSON string over this socket.
2. **AM Broadcast Fallback**: If the socket connection fails, it falls back to using Android's `am broadcast` command. It constructs an intent targeting `com.termux.api/.TermuxApiReceiver` and attaches the socket addresses, arguments, and process metadata as Intent extras (`--es`).

### Android 14 Workaround

On Android 14 (API level 34) and newer, Android aggressively freezes cached processes. If the `Termux:API` process is frozen, connecting to its `com.termux.api://listen` socket will succeed but reading from it will block indefinitely. To avoid this hang, `termux-api` completely skips the socket attempt on Android 14+ and unconditionally uses the `am broadcast` method, since sending a broadcast intent signals the Android system to unfreeze the receiver.
