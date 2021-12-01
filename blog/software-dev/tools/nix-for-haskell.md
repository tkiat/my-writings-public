# Nix: Using It for Haskell Development

Nix is a reproducible package manager. It can manage any dependencies, unlike Stack that can manage only Haskell packages. The syntax for Nix is hard to grasp because the abstraction level is very high but I can ~~copy~~ take the inspiration from [someone else's code](https://github.com/utdemir/hs-nix-template) then adapt to my own need. Seriously, this works really well as only package names are different.

## Run GHCi Against Compiled Packages

Nix helps you include packages in GHCi given that it is available with Nix. To find a Haskell package in Nixpkgs, you can query all the results and save them to a file. You can then search for a package in that file.

```bash
$ nix-env -f '<nixpkgs>' -qaP -A haskellPackages > temp
```

To find a `random` package, search for that keyword in the second column.

```bash
$ cat temp
```

Create `shell.nix` with the below content to add a `random` package.

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

Create a Nix shell and begin your journey

```bash
$ nix-shell
$ ghci
```

## Create Haskell Project with Nix

I am going to use Cabal here, not Stack. I name my project here `pomodoro-bar`.

### Create a Minimal Main.hs

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

synopsis:            A pausable and configurable Pomodoro timer with stats.
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

niv will generate `nix/sources.json` and `nix/sources.nix` to fix the version of Nixpkgs. This is very crucial as different Nixpkgs versions will contain different sets of packages. I prefer the current stable branch which is version 21.05.

```bash
$ niv init
$ niv update nixpkgs --branch nixos-21.05
```

### Create a Minimal default.nix

I am using `ghc8107` here because that is the version in the 21.05 branch.

```nix
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
```

### Create shell.nix

`shell.nix` will transfer us to the Nix shell, which contains all the environmental variables and packages declared in `default.nix`. We can refer to `default.nix` to reuse global variables over there.

```nix
(import ./default.nix { }).shell
```
<center>shell.nix</center>

### Add More buildInputs (Optional)

```nix
buildInputs = [
  pkgs.haskellPackages.cabal-install
  pkgs.haskellPackages.cabal-fmt
  pkgs.haskellPackages.haskell-language-server
  pkgs.haskellPackages.ormolu
  pkgs.nixpkgs-fmt
];
```

### Add a Local Hoogle Server (Optional)

Hoogle is a search engine for Haskell API. It is available over the internet but the local server binds with the exact version of the local packages you are using.

Note: I faced a [bug](https://github.com/NixOS/nixpkgs/issues/33592) where I needed to add a fancy package apart from `base` to make it work.

### Add exe field

You want to build the executable at the end. Add this to `default.nix`.

```nix
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
```

You can then build the project using `nix-build --attr exe`. You can use Makefile if you don't want to remember the command.

### Add Docker (Optional)

This will build a docker image. The coworkers can therefore execute in the same environment as yours. Some companies use Docker for deployment and some devs only know Docker so having this option is sometimes crucial.

```nix
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
```

### Done!

Run the following commands and you should get "Hello World!".

```bash
$ nix-build --attr exe
$ ./result/bin/pomodoro-bar
```
