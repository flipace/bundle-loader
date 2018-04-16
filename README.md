[![npm][npm]][npm-url]

This bundle loader is a fork of the official bundle-loader for webpack with an added errorCb
to handle chunk loading errors.

<h2 align="center">Install</h2>

```bash
npm i @flipace/bundle-loader --save
```

<h2 align="center"><a href="https://webpack.js.org/concepts/loaders">Usage</a></h2>

**webpack.config.js**
```js
module.exports = {
  module: {
    rules: [
      {
        test: /\.bundle\.js$/,
        use: '@flipace/bundle-loader'
      }
    ]
  }
}
```

The chunk is requested, when you require the bundle.

**file.js**
```js
import bundle from './file.bundle.js';
```

To wait until the chunk is available (and get the exports)
you need to async wait for it.

```js
bundle((file) => {
  // use the file like it was required
  const file = require('./file.js')
}, (err) => { console.log('An error occurred!', err); });
```

This wraps the `require('file.js')` in a `require.ensure` block

Multiple callbacks can be added. They will be executed in the order of addition.

```js
bundle(callbackTwo)
bundle(callbackThree)
```

If a callback is added after dependencies were loaded, it will be called immediately.

<h2 align="center">Options</h2>

|Name|Type|Default|Description|
|:--:|:--:|:-----:|:----------|
|**`lazy`**|`{Boolean}`|`false`|Loads the imported bundle asynchronously|
|**`name`**|`{String}`|`[id].[name]`|Configure a custom filename for your imported bundle|

### `lazy`

The file is requested when you require the `bundle-loader`. If you want it to request it lazy, use:

**webpack.config.js**
```js
{
  loader: 'bundle-loader',
  options: {
    lazy: true
  }
}
```

```js
import bundle from './file.bundle.js'

bundle((file) => {...})
```

> ℹ️  The chunk is not requested until you call the load function

### `name`

You may set name for a bundle using the `name` options parameter.
See [documentation](https://github.com/webpack/loader-utils#interpolatename).

**webpack.config.js**
```js
{
  loader: 'bundle-loader',
  options: {
    name: '[name]'
  }
}
```

> :warning: chunks created by the loader will be named according to the
[`output.chunkFilename`](https://webpack.js.org/configuration/output/#output-chunkfilename) rule, which defaults to `[id].[name]`. Here `[name]` corresponds to the chunk name set in the `name` options parameter.

<h2 align="center">Examples</h2>

```js
import bundle from './file.bundle.js'
```

**webpack.config.js**
``` js
module.exports = {
  entry: {
   index: './App.js'
  },
  output: {
    path: path.resolve(__dirname, 'dest'),
    filename: '[name].js',
    // or whatever other format you want
    chunkFilename: '[name].[id].js',
  },
  module: {
    rules: [
      {
        test: /\.bundle\.js$/,
        use: {
          loader: 'bundle-loader',
          options: {
            name: 'my-chunk'
          }
        }
      }
    ]
  }
}
```

Normal chunks will show up using the `filename` rule above, and be named according to their `[chunkname]`.

Chunks from `bundle-loader`, however will load using the `chunkFilename` rule, so the example files will produce `my-chunk.1.js` and `file-2.js` respectively.

You can also use `chunkFilename` to add hash values to the filename, since putting `[hash]` in the bundle options parameter does not work correctly.

<h2 align="center">Maintainers</h2>
