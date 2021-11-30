# Using Nix for Haskell Development
j
Nix is a reproducible package manager. It can manage any dependencies unlike Stack that can manage only Haskell packages. The syntax for Nix is hard to grasp because the abstraction level is very high but I simply copy and paste the code then adapt to my own need. Seriously, this works really well as the difference is usually only package names.

## Run GHCi Against Compiled Packages

Nix helps you include packages in GHCi given that it is available with Nix. To find haskell package in nixpkgs, you can query all the result and save to a file. You can then search for a package in that file.

```bash
$ nix-env -f '<nixpkgs>' -qaP -A haskellPackages > temp
```

To find a `random` package, search for that keyword in the second column.

```bash
$ cat temp
```

Create `shell.nix` with below content to add a `random` package.

```nix
{ pkgs ? import <nixpkgs> {} }:

let
  myGhc = pkgs.haskellPackages.ghcWithPackages (hpkgs: with hpkgs; [
    random
  ]);
in
pkgs.mkShell {
  buildInputs = [myGhc];
}
```
<center>shell.nix</center>

Create Nix shell and begin your journey

```bash
$ nix-shell
$ ghci
```

## Create Haskell Project with Nix

I am going to use Cabal here, not Stack. I name my project here as pomodoro-bar.

### Create Miminal Main.hs

```haskell
module Main where

main :: IO ()
main = putStr "Hello World!"
```

### Create Minimal pomodoro-bar.cabal

```cabal
cabal-version:       3.0
name:                pomodoro-bar
version:             0.1.0.0

synopsis:            A pausable and configurable pomodoro timer with stats.
license:             GPL-2.0-only

author:              Theerawat Kiatdarakun
copyright:           Theerawat Kiatdarakun
maintainer:          tkiat@tutanota.com

build-type:          Simple

executable pomodoro-bar
  main-is:            Main.hs
  default-language:   Haskell2010
  ghc-options:        -threaded -O2
  build-depends:      base
```

### Use niv to Fix Nixpkgs Version

This is very crucial as different Nixpkgs versions will contain different sets of packages. I prefer the current stable branch which is of version 21.05.

```bash
$ niv init
$ niv update nixpkgs --branch nixos-21.05
```

1. now create minimal efault.nix with this content

{}:

let
  sources = import ./nix/sources.nix;
  pkgs = import sources.nixpkgs { };

  gitignore = pkgs.nix-gitignore.gitignoreSourcePure [ ./.gitignore ];

  myHaskellPackages = pkgs.haskell.packages.ghc8107.override {
    overrides = hself: hsuper: {
      "pomodoro-bar" =
        hself.callCabal2nix
          "pomodoro-bar"
          (gitignore ./.)
          { };
    };
  };

  shell = myHaskellPackages.shellFor {
    packages = p: [
      p."pomodoro-bar"
    ];
    buildInputs = [
      pkgs.haskellPackages.cabal-install
    ];
  };

in
{
  inherit shell;
  inherit myHaskellPackages;
  "pomodoro-bar" = myHaskellPackages."pomodoro-bar";
}


it is ghc8107 because that is the version in nixos-21.05
it is 8107 because compiler ver

1. instead of creating a new shellnix, we refer to defaultnix since we can refer the the global variables in default.nix whciih is handy. now create shjell.nix

(import ./default.nix { }).shell

1. run nix-shell . it should work

1. add more vuildInputs.

    buildInputs = [
      pkgs.haskellPackages.cabal-install
      pkgs.haskellPackages.cabal-fmt
      pkgs.haskellPackages.haskell-language-server
      pkgs.haskellPackages.ormolu
      pkgs.nixpkgs-fmt
    ];

1. add Hoogle. you can use iternet but advantage is hoolde local will bind with your package version using.  for hoolge thereis a bug (error: value is null while a set was expected) https://github.com/NixOS/nixpkgs/issues/33592  you need to ad some fancy pakcages aprt from base. you can do this later as you develop the project. for a short while use internet resources

1. add exe. you want to build package into an executabe

...
let
	...
  exe = pkgs.haskell.lib.justStaticExecutables (myHaskellPackages."pomodoro-bar");
	...
in
{
  ...
  inherit exe;
  ...
}

when build use `nix-build --attr exe`. you can use makefile if you dont want to remember

1. add docker. this will build docker imahe. the coworkers can therefore execute in the same environment as yours. so why we already have nix but docker? 1. someone might be familiar with docker. 2. docker image also work like nix . the advantage of nix is reproducible build when you want to build docker image again it will be identical otherwise they almost have the same use

...
let
	...
  docker = pkgs.dockerTools.buildImage {
    name = "pomodoro-bar";
    config.Cmd = [ "${exe}/bin/pomodoro-bar" ];
  };
	...
in
{
  ...
  inherit docker;
  ...
}

1. Done! test by nix-build --attr exe then ./result/bin/pomodoro-bar you should get hello world
