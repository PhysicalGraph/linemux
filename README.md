
# linemux

[![Build Status](https://img.shields.io/github/actions/workflow/status/jmagnuson/linemux/test.yml?branch=master)](https://github.com/jmagnuson/linemux/actions)
[![Crate](https://img.shields.io/crates/v/linemux.svg)](https://crates.io/crates/linemux)
[![API](https://docs.rs/linemux/badge.svg)](https://docs.rs/linemux)
[![Coverage](https://codecov.io/gh/jmagnuson/linemux/branch/master/graph/badge.svg)](https://codecov.io/gh/jmagnuson/linemux)

A library providing asynchronous, multiplexed tailing for (namely log) files.

Also available is the underlying file event-stream (driven by [`notify`](https://crates.io/crates/notify))
that can register non-existent files.

## Usage

Add linemux to your `Cargo.toml` with:

```toml
[dependencies]
linemux = "0.3"
```

## Example

```rust,no_run
use linemux::MuxedLines;

#[tokio::main]
async fn main() -> std::io::Result<()> {
    let mut lines = MuxedLines::new()?;

    // Register some files to be tailed, whether they currently exist or not.
    lines.add_file("some/file.log").await?;
    lines.add_file("/some/other/file.log").await?;

    // Wait for `Line` event, which contains the line captured for a given
    // source path.
    while let Ok(Some(line)) = lines.next_line().await {
        println!("source: {}, line: {}", line.source().display(), line.line());
    }
    Ok(())
}
```

## Caveats

Currently, linemux assumes that if a nonexistent file is added, its parent does
at least exist to register a directory watch with `notify`. This is done for
performance reasons and to simplify the pending-watch complexity (such as
limiting recursion and fs event spam). However, this may change if a need
presents itself.

## Minimum Supported Rust Version (MSRV)

This crate is guaranteed to compile on stable Rust 1.60 and up.

## License

Licensed under either of

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or
  https://www.apache.org/licenses/LICENSE-2.0)
- MIT license ([LICENSE-MIT](LICENSE-MIT) or https://opensource.org/licenses/MIT)

at your option.

## Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted
for inclusion in the work by you, as defined in the Apache-2.0 license, shall be
dual licensed as above, without any additional terms or conditions.
