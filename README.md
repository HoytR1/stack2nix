# stack2nix

[![Build Status](https://travis-ci.org/input-output-hk/stack2nix.svg?branch=master)](https://travis-ci.org/input-output-hk/stack2nix)

## About

`stack2nix` automates conversion from [Stack](https://docs.haskellstack.org/en/stable/README/) configuration file to [Nix](http://nixos.org/nix/) expressions.

`stack2nix` high-level workflow:

- invoke `stack list-dependencies` to determine complete fixed version list of packages based on resolver
- apply any additional configuration (local packages, extra dependencies, etc) from `stack.yaml`
- generate complete list of dependencies to Nix expressions, replacing upstream `hackage-packages.nix`

## Installation

There are two options. The first is to install stack2nix and its dependencies on your machine directly, and the second is to use the supported virtual machine configuration.

If there are difficulties please file an issue. Generally the virtual machine approach should be more reliable.

### Native Environment

1. Install [nix](https://nixos.org/nix/).
2. Clone this repo.
3. Run `stack install` to install.
4. Ensure `cabal` (via `cabal-install` package), `cabal2nix` >= 2.2.1, and `stack2nix` are in your `$PATH`.

### Virtual Machine

1. Install [VirtualBox](https://www.virtualbox.org/wiki/VirtualBox) and [Vagrant](https://www.vagrantup.com/).
2. Clone this repo.
3. Run `./scripts/vagrant.sh` and take a coffee break.
4. If there are no errors, log into the VM: `vagrant ssh`.

## Usage

Nix expressions generated by stack2nix depend on a somewhat recent upstream change to nixpkgs. If `nix-env` or `nix-build` fails with an error mentioning `initialPackages` or `commonPackages`,
try setting the `NIX_PATH` environment variable: `NIX_PATH=nixpkgs=https://github.com/NixOS/nixpkgs/archive/a905b7cd0c2dc0714195a50bf176cd8e4593502d.tar.gz`.

### Remote Packages

Stack2nix can generate a nix expressions for Haskell packages hosted in git repositories.

```
    $ stack2nix --revision 242e2a064f6a32b22e1599bbfe72e64d7b6203b8 https://github.com/jgm/pandoc.git > demo.nix
    $ nix-build -A pandoc demo.nix
```

### Local Packages

Sometimes it's convenient to build local Haskell packages. Assuming the current directory is a locally maintained fork of Pandoc:

```
    $ stack2nix . > default.nix
    $ nix-build -A pandoc
```

## Testing

Run `./scripts/travis.sh` to build and test.
