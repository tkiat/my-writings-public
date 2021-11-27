# How to Run npm Packages Locally

You can install an npm package globally

```bash
npm -g purescript spago
```

However, this can be troublesome because a globally installed package in your machine might not be the same as another when you hand over the project to him as it might not be compatible with the project dependencies.

It is therefore advisable to install all packages locally in the project. This will update package.json and another person who clones your project can install compatible versions from that package.json. To install locally, fun

so we need to install locally to the project

```bash
npm install --save-dev purescript spago
```

Executing the command will still give you an error.

```bash
$ spago
bash: spago: command not found
```

That is because, as with other programs, spago is not found in $PATH so it cannot execute it. You need to tun this instead.

```bash
$(npm bin)/spago
```

Now it is recognized. One interesting is that running

```bash
$(npm bin)/spago init
```

will output an error since it cannot find command `purs`. It is from the `purescript` package that we just installed along with the `spago` package. So, what can we do?

We can simply modify the `package.json`

```json
{
  "scripts": {
    "spago": "spago"
  }
}
```

`npm run` will take everything in the context of the local packages. This means `npm run spago init` will refer to both `spago` and `purs` commands from the local dependencies, not your machine.
