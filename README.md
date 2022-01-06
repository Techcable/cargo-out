cargo-out ![crates.io](https://shields.io/crates/v/cargo-out)
=============
A cargo subcommand to fetch the [`$OUT_DIR` variable](https://doc.rust-lang.org/cargo/reference/build-scripts.html#outputs-of-the-build-script) from build scripts.

This is extremely useful to inspect the output of automatically generated code, like [bindgen](https://rust-lang.github.io/rust-bindgen/) or [parol](https://lib.rs/crates/parol).

This can be seen as an extension to `cargo metadata`, except it effectively requires that build scripts succed.

If `cargo check` succeeds, then this command should succeed to (and give the same output).

Due to recent changes in `cargo check`, this *will not invalidate cached outputs*, so the build wont be re-run unless it needs to.

## Examples
#### `$ cargo out`
Assuming your current crate is named `current-crate`, this wil output something like:
````
current-crate /Users/techcable/git/current-crate/target/debug/build/current-crate-82e5bb1cb82b68a7/out
````

Alternatively, to omit the name you can use `--no-names`. This is useful in shell scripts as `$(cargo out --no-names)`

This command works with any crate name (as long as it has a `build.rs` file).

#### `$ cargo out syn indexmap` 
````
syn /Users/techcable/git/current-crate/target/debug/build/syn-2bbc24a01fc81726/out
indexmap /Users/techcable/git/current-crate/target/debug/build/indexmap-376e9f234cf30ee8/out
````

These are output in the order specified on the command line, seperated by newlines.

If the package doesn't have an out dir, the output will be "<MISSING OUT_DIR>" exactly and the exit code will be `2`.


You might want to consider json output as well.
#### `$ cargo out --json --all` (or `cargo out --json --workspace` for only [workspace](https://doc.rust-lang.org/cargo/reference/workspaces.html) packages)
`````json
{
    "syn": "/Users/techcable/git/current-crate/target/debug/build/syn-2bbc24a01fc81726/out",
    "indexmap": "/Users/techcable/git/current-crate/target/debug/build/indexmap-376e9f234cf30ee8/out",
    // other_crates here
}
`````

If packages don't have an $OUT_DIR (because they don't have a build script), then the value for the specified key will be null. 

This can be used along with [jq](https://stedolan.github.io/jq/) for easy processing in scripts :)

Other information like package versions are excluded, because that can easily be retrieved from `cargo metadata`.

## How it works
This runs `cargo check --message-format=json` and extracts only the nessicarry information.


Historically this has been an issue for IDEs, both [intellij-rust](https://github.com/intellij-rust/intellij-rust/pull/4542) and [rust-analsyer](https://github.com/rust-analyzer/rust-analyzer/pull/1967) have struggled to support this.


Do to recent compiler changes, this will always output the proper [`$OUT_DIR` variables](https://doc.rust-lang.org/cargo/reference/build-scripts.html#outputs-of-the-build-script) for all packages that have one.


However recent changes to `cargo check` have made this possible. It sill  :)

