---
layout: post
title: Upgrading to Webpack 4
---

Upgrading to Webpack 4 can be accomplished with a few steps. Some of these steps are more difficult and harder to understand than others. Below are some of the solutions I've used to migrate Webpack configs in the past. 

## 1. Upgrade Webpack and any used loaders and install Webpack-CLI
```bash
npm install --save-dev webpack@latest webpack-cli@latest babel-loader ...any other loaders...
```
## 2. Use `mode`

The quickest win for your bundling process is to use the `mode` flag.
```js
module.exports = {
    mode: 'production',
    ...
};
```
By default, `mode` is set to `production` so just by upgrading your automatically winning in improving your bundle's performance. There's a few gotchas, like Production mode doesn't output any sourcemaps and auto sets some things around the UglifyJsPlugin, but feel free to add those back in as your app needs. You'll most likely remove most of your included webpack plugins.

```js
module.exports = {
  ...
  optimization: {
    minimizer: [
      new UglifyJsPlugin({
        sourceMap: true,
        ...
      }),
    ],
  },
  ...
};
```

## 3. `optimization` flag
Code splitting is a huge benefit with using a bundler and Webpack let's you fine tune your code splitting _a lot_. This has to be one of the bigger gotcha's every time I upgrade a Webpack config. There are a ton of options you can do to manage how your code splitting works and it really comes down to what you prefer. By default, Webpack tries to help you out with this as much as possible, but sometimes fine tuning is the best case. [This](https://medium.com/webpack/webpack-4-code-splitting-chunk-graph-and-the-splitchunks-optimization-be739a861366) is a great overview of what this option does and how you can tweak it for exactly what you need. Below are a few implementations that I have found useful for very specific projects.

Take all node_modules and put them in a separate bundle
```js
module.exports = {
  ...
  optimization: {
    splitChunks: {
      cacheGroups: {
        vendor: {
          test: /node_modules\/.*/,
          name: 'vendors',
          chunks: 'all',
        },
      },
    },
  },
};
```

Or, take a very specific list of node_modules and put them in a separate bundle for use throughout the application
```js
module.exports = {
  entry: {
    commonModules: [
      node_module1,
      node_module2,
    ],
    ...,
  },
  optimization: {
    splitChunks: {
      cacheGroups: {
        commonModules: {
          chunks: 'initial',
          enforce: true,
          name: 'commonModules',
          test: 'commonModules',
        },
      },
    },
  },
  ...,
};
```

## 4. Removing the `ExtractTextPlugin` for creating CSS files
As of writing, the ExtractTextPlugin is deprecated and doesn't work with Webpack 4. You'll want to switch to the `MiniCssExtractPlugin` for extracing your CSS files.

Previously you might have something like below:
```js
const ExtractTextPlugin = require('extract-text-webpack-plugin');	
module.exports = {
  module: {
    rules: [
      {
        test: /\.s?css$/,
        use: ExtractTextPlugin.extract({
          fallback: 'style-loader',
          use: [
            'css-loader',
          ],
        }),
      },
    ],
  },
  plugins: [
    new ExtractTextPlugin({
      filename: '[hash].css'
    }),	
  ],
};
```
You'll upgrade to 
```js
const MiniCssExtractPlugin = require('mini-css-extract-plugin');
module.exports = {
  module: {
    rules: [
      {
        test: /\.s?css$/,
        use: [
          MiniCssExtractPlugin.loader,
          'css-loader',
        ],
      },
    ],
  },
  plugins: [
    new MiniCssExtractPlugin({
      filename: '[hash].css',
    }),
  ],
};
```

## 5. Run the build command and see what breaks!

Most likely it's related to an non-upgraded loader. If your migrating from v1 of Webpack there'll be quite a lot for you to do and it will require quite a bit of work, beyond what is outlined in this post. V2 and v3 upgrades should be smoother, especially if you include some of the gotchas from above. 

As always, reach out to me on [twitter](https://twitter.com/fuhton) if you have any questions.

Some good links:
* [Webpack Changlog](https://github.com/webpack/webpack/wiki/Changelog-WIP)
* [Webpack Code Splitting docs](https://webpack.js.org/guides/code-splitting/)
* [One dev's play by play when upgrading](https://gist.github.com/gricard/e8057f7de1029f9036a990af95c62ba8)
