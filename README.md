# Status

This project has been **SUPERSEDED**
by [`trust`](https://github.com/japaric/trust), another CI template, and won't
received updates or bug fixes.

## Why

`trust` makes things simpler and better because it's based on
the [`cross`](https://github.com/japaric/cross) tool. Among the advantages we
have:

- It's easy to run the test script locally (on Linux). No need to install cross
  toolchains or other foreign stuff, simply install `cross` and, for example,
  call `TARGET=aarch64-unknown-linux-gnu sh ci/script.sh`.

- Painless upgrades. Most upgrades, to get support for new targets or to fix
  cross compilation problems related to C dependencies, will pretty much only
  involve [bumping the version of the `cross` tool][bump] rather than more
  invasive changes to `.travis.yml` or to the scripts in the `ci` directory.

[bump]: https://github.com/japaric/trust/blob/v0.1.0/ci/install.sh#L20

- Support for more targets / architectures. `trust` supports x86, ARM, MIPS,
  PowerPC and SystemZ.

- The CI scripts are simpler as the requirements for cross compilation are
  handled by `cross` and don't show up in the CI scripts.

-- [@japaric](https://github.com/japaric), 2017-01-03

---

[![Travis](https://travis-ci.org/japaric/rust-everywhere.svg?branch=master)](https://travis-ci.org/japaric/rust-everywhere)
[![Appveyor](https://ci.appveyor.com/api/projects/status/d37xqtcx5ct9fyfr?svg=true)](https://ci.appveyor.com/project/japaric/rust-everywhere)

# `rust-everywhere`

> Use CI services to generate binary releases of your Rust program for Linux, Mac and Windows

This repository has configured [Travis CI] and [AppVeyor] to generate **[binary releases]**, in both
tarball/zipfile and deb (\*) format, of a Cargo project (in this example, a variation of hello
world) for all the [tier 1] platforms and some lower tier platforms whenever a new git tag is
pushed.

[Travis CI]: https://travis-ci.org/
[AppVeyor]: https://www.appveyor.com/
[binary releases]: https://github.com/japaric/rust-everywhere/releases
[tier 1]: https://doc.rust-lang.org/book/getting-started.html#tier-1

(\*) .deb support is minimal right now: you can only package binaries and not, for example, store
information about dependencies. If you need more features in this area open an issue to let me know!

## Supported targets

The current CI configuration builds, tests and generates binary releases for the following targets:

- `aarch64-unknown-linux-gnu`. (Linux on 64-bit ARM) **WARNING** Experimental target. Tests are
executed using qemu user emulation, but this approach has problems when too many threads are
spawned.
- `armv7-unknown-linux-gnueabihf` (Linux on 32-bit ARM). **WARNING** Experimental target. Tests are
executed using qemu user emulation, but this approach has problems when too many threads are
 spawned.
- `i686-apple-darwin` (32-bit OSX)
- `i686-pc-windows-gnu` (32-bit Windows, MinGW)
- `i686-pc-windows-msvc` (32-bit Windows, MSVC)
- `i686-unknown-linux-gnu` (32-bit Linux)
- `i686-unknown-linux-musl`. (32-bit Linux, statically linked binaries)
- `x86_64-apple-darwin` (64-bit OSX)
- `x86_64-pc-windows-gnu` (64-bit Windows, MinGW)
- `x86_64-pc-windows-msvc` (64-bit Windows, MSVC)
- `x86_64-unknown-linux-gnu` (64-bit Linux)
- `x86_64-unknown-linux-musl`. (64-bit Linux, statically linked binaries)

## How to use

You can use this CI configuration, as a starting point, in your project by copying the `.travis.yml`
and `appveyor.yml` files and the `ci` directory in your project repository. And then customizing
the configuration for your needs by implementing all the `TODO`s contained in those files.

All these aspects can be configured:

- Test on any combinations of these channels: stable, beta and nightly.
- Deploy on one of these channels: stable, beta and nightly.
- The contents of the release tarball/zipfile
- CI phases like `install` and `script`
- Metadata of the .deb packages.

Once configured, simply push a new git tag to build a new binary release:

``` sh
$ git tag v1.2.3
$ git push --tags
```

You should see the release tarballs/zipfiles under your project's ['releases'] page.

['releases']: https://github.com/japaric/rust-everywhere/releases

## How do I install/uninstall these binary releases?

Examples:

### Tarball/zipfiles

```
# Install
$ curl -OL https://github.com/japaric/rust-everywhere/releases/download/v0.1.41/rust-everywhere-v0.1.41-x86_64-unknown-linux-gnu.tar.gz
$ tar xzf rust-everywhere-v0.1.41-x86_64-unknown-linux-gnu.tar.gz
$ sudo cp hello /usr/local/bin/

# Test the program
$ hello
0.1.41: x86_64-unknown-linux-gnu says hello!
Compiled with rust-1.8.0 (Stable channel)

# Uninstall
$ sudo rm /usr/local/bin/hello
```

### .deb packages

```
# Install
$ curl -OL https://github.com/japaric/rust-everywhere/releases/download/v0.1.41/rust-everywhere-v0.1.41-x86_64-unknown-linux-gnu.deb
$ dpkg -i rust-everywhere-v0.1.41-x86_64-unknown-linux-gnu.deb
Selecting previously unselected package rust-everywhere.
(Reading database ... 12398 files and directories currently installed.)
Preparing to unpack rust-everywhere-v0.1.41-x86_64-unknown-linux-gnu.deb ...
Unpacking rust-everywhere (0.1.41) ...
Setting up rust-everywhere (0.1.41) ...

# Test the program
$ hello
0.1.41: x86_64-unknown-linux-gnu says hello!
Compiled with rust-1.8.0 (Stable channel)

# Uninstall
# dpkg -r rust-everywhere
(Reading database ... 12399 files and directories currently installed.)
Removing rust-everywhere (0.1.41) ...
```

## Using a binary release with Travis CI

If you want to use a binary release generated by rust-everywhere (to save time by not having to call
`cargo install`) on a Travis build job, you can use the `install.sh` script contained in this
repository and use it like this in your Travis configuration:

```
install:
  - curl -sf "https://raw.githubusercontent.com/japaric/rust-everywhere/master/install.sh" | bash -s -- --from azerupi/mdBook --tag 0.0.11-rc1
```

Check the script for more information about its usage. The script is not very good at handling
errors so use it with care ;-).

## Known issues

- The Cargo project must be hosted on GitHub. I know GitLab also has a "releases" feature but I
    don't know if it can be integrated with Travis CI and/or AppVeyor. AFAICT Bitbucket has no
    "releases" equivalent. And IDK about other git hosting solutions.

## License

Licensed under either of

- Apache License, Version 2.0 ([LICENSE-APACHE](LICENSE-APACHE) or
  http://www.apache.org/licenses/LICENSE-2.0)
- MIT license ([LICENSE-MIT](LICENSE-MIT) or http://opensource.org/licenses/MIT)

at your option.

### Contribution

Unless you explicitly state otherwise, any contribution intentionally submitted for inclusion in the
work by you, as defined in the Apache-2.0 license, shall be dual licensed as above, without any
additional terms or conditions.
