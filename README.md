# Lilyrs

Lilyrs is a Rust workspace for application infrastructure, framework components,
and integrations.

- `crates/foundation`: shared infrastructure and application contracts.
- `crates/framework`: HTTP, WebSocket, and consumer frameworks.
- `crates/integrations`: database, messaging, and other integrations.
- `crates/lilyrs`: the application-facing facade.

Full documentation and canonical usage are available at [lilyrs.com](https://lilyrs.com).
Each published crate includes its own README and both license texts; see its
README and API reference for the supported public surface.
The [connected examples](examples/README.md) run HTTP, WebSocket and Consumer
applications with shared DI services, real databases, messaging and facade clients.
The [facade qualification guide](tests/qualification/FACADE.md) describes the
compatibility, regression and live example checks.

## Project history

Lilyrs grew out of the experimental repository.
The original repository is preserved as a historical archive of the project's
early development. Active development continues in this repository.

## Rust toolchain

Rust 1.96.1 is the currently supported and tested toolchain, pinned in
`rust-toolchain.toml`. All published crates use edition 2024 and inherit
`rust-version = "1.96.1"` from the workspace. Cargo treats this value as a
minimum compiler version; newer toolchains are not currently validated.

## Platform testing and compatibility

All testing to date has been performed on Linux. Test runs have not been
performed on Windows or macOS, so native behavior on those operating systems
has not been validated.

If you encounter a problem on Windows or macOS, please report it as a
[GitHub issue](https://github.com/lily-platform/lilyrs/issues), including your
operating system and architecture, Rust version, steps to reproduce, and
relevant error output.

If you run into problems on Windows or macOS, we recommend running Lilyrs
in a Linux Docker container. Docker Desktop runs Linux containers through a
Linux virtual machine, providing a Linux kernel for the application. The
[connected examples](examples/README.md) include a Docker Compose setup.

### Potential Windows file-path issue

Source inspection identified a potential Windows startup issue that has not
been tested on Windows. Protected `${file:...}` references in `lily_config`
and custom CA file readers in `lily_http_client`, `lily_redis`, and
`lily_websocket_redis` require the supplied path to match its canonical form.
On Windows, `std::fs::canonicalize` can turn `C:\secrets\token.txt` into
`\\?\C:\secrets\token.txt`, causing an existing file to be rejected.

This also affects applications built with `lily_http_api` or `lily_websocket`
when their configuration uses these file references or integrations. A failure
can prevent application startup. The main `lily.toml` file path itself is not
subject to this canonical-path equality check.

For this path-format mismatch, a temporary workaround is to supply the exact
path returned by `std::fs::canonicalize` for the existing secret or CA file.
When writing Windows paths in TOML, use single-quoted literal strings or escape
backslashes correctly. Alternatively, use the Linux Docker setup described
above. Please report any Windows failures through the issue link above.

## License

Lily is dual-licensed under either the [MIT License](LICENSE-MIT) or the
[Apache License, Version 2.0](LICENSE-APACHE), at your option.

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in Lily by you shall be dual-licensed as above, without any
additional terms or conditions.
