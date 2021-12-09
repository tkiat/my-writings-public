# Node: Run npm Packages Locally

You can install an npm package globally

```bash
$ npm -g purescript spago
```

However, this can be troublesome because a globally installed package in your machine might not be the same as another developer. The mismatch can impede the development process.

It is therefore advisable to install all packages locally in the project for the reproducible sake. To install locally, execute

```bash
$ npm install --save-dev purescript spago
```

Executing the command will still give you an error.

```bash
$ spago
bash: spago: command not found
```

That is because, as with other programs, spago is not found in $PATH so it cannot execute it. You need to add the executable in the `node_modules` folder in $PATH or run this instead.

```bash
$(npm bin)/spago
```

Now it is recognized. One interesting is that running

```bash
$(npm bin)/spago init
```

will output an error since it cannot find command `purs` (its dependency). It is from the `purescript` package that we just installed along with the `spago` package. So, what can we do?

We can simply modify `package.json`

```json
{
  "scripts": {
    "spago": "spago"
  }
}
```

`npm run` will take everything in the context of the local packages. This means `npm run spago init` will refer to both `spago` and `purs` commands from the local dependencies.
