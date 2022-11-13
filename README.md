# die-exit-2

![GitHub](https://img.shields.io/github/license/SeriousBug/die)
![GitHub Workflow Status](https://github.com/SeriousBug/die/workflows/tests/badge.svg)
[![Latest Version](https://img.shields.io/crates/v/die-exit-2.svg)](https://crates.io/crates/die-exit-2)
[![Documentation](https://docs.rs/die-exit-2/badge.svg)](https://docs.rs/die-exit-2)

[die-exit-2] is a simple Rust library to make it easy to handle errors and exit in command line programs.

It is forked from [die-exit], which in turn is a fork of [die]. The fork only
exists to publish the updated version of `die-exit`: the code ha updates that
were never published to crates.io. I needed an updated version on `crates.io` so
I could publish a crate that depends on it, so I'm publishing this fork. This
fork will be deprecated if the original crate publishes an updated version at
some point.

[die]: https://code.moparisthebest.com/moparisthebest/die
[die-exit]: https://github.com/Xavientois/die

## Cargo.toml

```toml
[dependencies]
die-exit-2 = "0.4"

# When added, `die!()` will trigger a panic rather than exiting during development.
[dev-dependencies.die-exit-2]
version = "0.4"
features = ["test"]
```

## Example usage:

```rust
use die_exit_2::*;
// Result:
Ok(1).die("no number"); // unwraps to 1 successfully
Err("failure").die("strange error"); // prints `strange error` to stderr then exits with code 1

// Option:
Some(1).die("no number"); // unwraps to 1 successfully
None.die("none option"); // prints `none option` to stderr then exits with code 1

// custom error codes:
Err("failure").die_code("strange error", 4); // prints `strange error` to stderr then exits with code 4
None.die_code("none option", 5); // prints `none option` to stderr then exits with code 5

// with function (Result only):
Err("failure").die_with(|error| format!("strange error: {}", error)); // prints `strange error: failure` to stderr then exits with code 1
Err("failure").die_with(|error| (format!("strange error: {}", error), 6)); // prints `strange error: failure` to stderr then exits with code 6

// die! macro:
die!("argument to -e must be numeric"); // prints message to stderr then exits with code 1
die!(2; "argument to -e must be numeric"); // prints message to stderr then exits with code 2
die!("argument to -e must be numeric"; 3); // prints message to stderr then exits with code 3
die!("argument {} must be {}", "-e", 1; 4); // prints `argument -e must be 1` to stderr then exits with code 4
die!("argument {} must be {}", "-e", 1); // prints `argument -e must be 1` to stderr then exits with code 1
die!(2); // prints nothing, only exits with code 3
die!(); // prints nothing, only exits with code 1
```

## Example testing:

Ensure that the `test` feature is turned on.

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use die_exit_2::*;

    #[test]
    #[should_panic]
    fn die_test() {
        die!("Die works in tests!"; exit_codes::COMPILER_ERROR);
    }
}
```

## `cargo` Features

- **test**: Turn this on if you want to run tests where `die` might be used.
  This will change the behaviour of `die` and its variants to call `panic!()`
  instead of `process::exit()`.

# License

This project is licensed under the MIT license ([LICENSE-MIT](LICENSE-MIT)
http://opensource.org/licenses/MIT)
