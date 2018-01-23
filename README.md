# test-es6-promise-webpack

attempted isolated reproduction for https://github.com/stefanpenner/es6-promise/issues/305

### TL;DR

~~Some people are having trouble with es6-promise and webpack, unfortunately I am
not able to reproduce. But this my attempt, and hopefully a starting point for
someone to help demonstrate a reproduction.~~

Reproduction: https://github.com/stefanpenner/test-es6-promise-webpack/commit/c0ee2d81db002823b79d9b5c9fd48bd41785e3cd

The problem is the fallback module `vertx`, available only on the `vertx` platform: http://vertx.io/, cannot be resolved by webpack, as it does not typically exist. Browserify works by allowing modules to [configure externals via their package.json](https://github.com/stefanpenner/es6-promise/blob/314e4831d5a0a85edcb084444ce089c16afdcbe2/package.json#L60-L62), unfortunately webpack does not (or appears not to).

### Reproduction Instructions

```sh
git clone https://github.com/stefanpenner/test-es6-promise-webpack.git
cd test-es6-promise-webpack
yarn; // must use yarn, to get the exact version of es6-promise (the one without the hacky work-around)
yarn install
yarn test
```

### Expected Output:

```
es6-promise was webpack'd
```
### Actual Output:

```
$ npx webpack --target=node src/index.js dist/bundle.js && node dist/bundle.js
Hash: 79ccfc4f3538d3537cd9
Version: webpack 3.10.0
Time: 79ms
    Asset     Size  Chunks             Chunk Names
bundle.js  32.4 kB       0  [emitted]  main
   [0] ./src/index.js 109 bytes {0} [built]
    + 1 hidden module

WARNING in ./node_modules/es6-promise/dist/es6-promise.js
Module not found: Error: Can't resolve 'vertx' in '/Users/spenner/src/stefanpenner/test-es6-promise-webpack/node_modules/es6-promise/dist'
 @ ./node_modules/es6-promise/dist/es6-promise.js 140:16-26
 @ ./src/index.js
es6-promise was webpack'd
```

## Potential Solutions

### Configure webpack

*didn't work, seems like webpack doesn't consider configuration from a module*
In theory, we can confgure webpack to correctly ignore `vertx` via something like the following, but I require a reproduction to confirm.

```js
// webpack.config.js
module.exports = {
  externals: {
    vertex: /** magic **/
  }
}
```


### Hackity Hack

**worked, but sub-optimal**

Some hackity hack to defeat webpack's static anaylizer
https://github.com/stefanpenner/es6-promise/pull/323/

```js
const vertx = Function('return this')().require('vertx');
```
