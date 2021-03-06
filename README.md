Rust overlay
============

[![Build Status](https://travis-ci.org/Heather/gentoo-rust.png?branch=master)](https://travis-ci.org/Heather/gentoo-rust) [![Join the chat at https://gitter.im/gentoo/rust](https://badges.gitter.im/Join%20Chat.svg)](https://gitter.im/gentoo/rust?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

[![tip for next commit](https://tip4commit.com/projects/43125.svg)](https://tip4commit.com/github/Heather/gentoo-rust)
[![tip for next commit](http://prime4commit.com/projects/291.svg)](http://prime4commit.com/projects/291)

# User's guide

## Adding the overlay

This [overlay](https://wiki.gentoo.org/wiki/Overlay) is available through [layman](https://wiki.gentoo.org/wiki/Layman).

To use packages from it, add it with

```
layman -a rust
```

If you use [eix](https://wiki.gentoo.org/wiki/Eix) you may need to execute

```
eix-update
```

after it.

## Packages

This overlay contains Rust compiler and Rust related packages and is the primary place for developing of the Rust infrastructure on Gentoo.

As user you may be interested in these packages:

* `dev-lang/rust` Rust compiler built from sources
* `dev-lang/rust-bin` Binary packaged Rust compiler
* `dev-rust/cargo` Cargo rust package manager and build tool

There are other useful packages in this overlay, use `eix` or whatever else to learn about them.

## Rust compiler implementations

Different Rust versions can be installed simulatneously on Gentoo.
[Slots](https://devmanual.gentoo.org/general-concepts/slotting/) and
[eselect](https://wiki.gentoo.org/wiki/Project:Eselect) are used for this purpose.
To learn more, see [eselect-rust](https://github.com/jauhien/eselect-rust).

Useful `USE flags`.

### `dev-lang/rust`

* `doc` install documentation
* `system-llvm` use system LLVM (will dicrease compilation time)

### `dev-lang/rust-bin`

* `cargo-bundled` install bundled Cargo
* `doc` install documentation

# Developer's guide

## Contributing

Fork this repo and make a pull request. We are happy to merge it.

Please, make sure you've read [devmanual](https://devmanual.gentoo.org/).

Commit message should look like

```
[category/packagename] short decription

Long description
```

This makes reading history easier. GPG signing your changes is a good idea.

If you have push access to this repo it is a good idea to still create a pull request,
so at least one more person have reviewed your code.
Exceptions are trivial changes and urgent changes (that fix something completely broken).

## Slotting

Currently we have these slots for `dev-lang/rust`:

* `1.0` -- 1.0 release
* `nightly` -- nightly version
* `git` -- upstream git version

This will probably change to some other schema that corresponds to current release channels (a subject to discussion).

Note, that source packages use a custom postfix for Rust libraries.
This is important, as otherwise simultaneously installed different Rust versions will fail to work.
An example of `src_prepare` that sets appropriate postfixes:

```
src_prepare() {
	local postfix="gentoo-${SLOT}"
	sed -i -e "s/CFG_FILENAME_EXTRA=.*/CFG_FILENAME_EXTRA=${postfix}/" mk/main.mk || die
}
```

For `dev-lang/rust-bin` we have no slots currently.

## eselect-rust

Rust compiler packages use [eselect-rust](https://github.com/jauhien/eselect-rust) to managed their symlinks.
Consult its README for information on how to properly register your package in eselect.

You need to set active Rust version in `pkg_postinst` if no one were set before:

```
eselect rust update --if-unset
```

You need to unset active Rust version in `pkg_postrm` if it were the one you just removed:

```
eselect rust unset --if-invalid
```

## Environment

You need to set `MANPATH` and `LDPATH` appropriately. See existing ebuilds.

## Testing

Please, make sure you have checked this before creating pull request:

* you've run `repoman full -d` and it didn't complain about errors
* you've emerged ebuild with your changes and it was installed correctly
* you've run the stuff your ebuild installed and it worked for you
* if you have keywords in your ebuild, you have tested it for every ARCH mentioned there

## Changes propagation to the tree

Changes in the packages available in the main Gentoo tree will propagate there after some time (usually one week).
At the moment these packages are:

* app-emacs/rust-mode
* app-eselect/eselect-rust
* app-shells/rust-zshcomp
* app-vim/rust-mode
* dev-lang/rust
* dev-lang/rust-binary
