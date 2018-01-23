# test-es6-promise-webpack

attempted isolated reproduction for https://github.com/stefanpenner/es6-promise/issues/305

### TL;DR

~~Some people are having trouble with es6-promise and webpack, unfortunately I am
not able to reproduce. But this my attempt, and hopefully a starting point for
someone to help demonstrate a reproduction.~~

Reproduction: https://github.com/stefanpenner/test-es6-promise-webpack/commit/c0ee2d81db002823b79d9b5c9fd48bd41785e3cd

The problem specifically, is the fallback module `vertx`, available on the `vertx` platform: http://vertx.io/


### Instructions

```sh
git clone https://github.com/stefanpenner/test-es6-promise-webpack.git
cd test-es6-promise-webpack
yarn;
yarn install
yarn test
```

### Expected (and actual) Output:

```
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

**worked**

Some hackity hack to defeat webpack's static anaylizer
https://github.com/stefanpenner/es6-promise/pull/323/

```js
const vertx = Function('return this')().require('vertx');
```
