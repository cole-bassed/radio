# Platform

## Target Platforms

- Windows
- Linux
- macOS

## General Rules

- Avoid OS-specific assumptions in shared code.
- Use platform-safe path utilities such as `dirs` or `directories`.
- Keep shell scripts optional and avoid depending on them for core workflows.

## Desktop Notes

- Use Tauri for the desktop shell.
- Expect platform-specific build dependencies, especially on Linux.
- Document any runtime requirements clearly.

## Web Notes

- The Dioxus web build should be portable and runtime-fetched.
- Globe visualization uses browser-side interop only where needed.

## CLI Notes

- Must run in Windows Terminal, Linux terminals, and macOS Terminal/iTerm2.
- Avoid ANSI assumptions that break basic legibility.
- Keep keyboard shortcuts simple and discoverable.

## Cross-Platform Risks

- Audio backend differences.
- WebKitGTK dependencies on Linux.
- File path differences.
- Packaging differences for desktop builds.

## Policy

If a feature needs platform-specific handling, isolate it behind a thin adapter and document it here.
