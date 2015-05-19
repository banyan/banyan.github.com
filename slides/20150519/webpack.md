title: webpack
output: webpack.html

--

# webpack

## Kohei Hasegawa (@banyan), 19 May 2015

--

# What is webpack?

--

### 1. Module bundler

```javascript
var $ = require('jquery');
```

* webpack lets us `require('modules')` in the browser by bundling up all of our dependencies.
* CommonJS syntax can be used to require modules

--

### 2. Compatible with many module patterns

* AMD
* CommonJS
* [UMD](https://github.com/umdjs/umd)
* ES6 modules

--

### 3. Compatible with many package manager

* npm
* bower (with [bower-webpack-plugin](https://github.com/lpiepiora/bower-webpack-plugin))
* component (with [component-webpack-plugin](https://github.com/webpack/component-webpack-plugin))

--

### 4. Build tool such as grunt/gulp/brunch

* It can build and bundle CSS/JS to 1 JS file
* Compile preprocessed JS and CSS

--

### 5. Plugins (loaders)

* Loaders are transformations that are applied on files.
* [List of loaders](https://github.com/webpack/docs/wiki/list-of-loaders). (These are just one of examples)
 * json-loader, script-loader, url-loader, coffee-loader, babel-loader, etc...

--

### 6. Dev server

* The webpack-dev-server is a little node.js Express server, which uses the webpack-dev-middleware to serve a webpack bundle.

--

# Usage

--

### Installation & CLI

```
npm install webpack -g

webpack <entry> <output>
webpack -p
webpack --config webpack.config.js
```

--

### Getting started

https://gist.github.com/banyan/f23a7910608ee643347d

--

### Okay, so webpack is awesome, isn't it?

<iframe src="http://giphy.com/embed/VtLkuCkObNC6c?html5=true" width="800" height="400" frameBorder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

--

# Browserify vs webpack

--

### It's controversial! :|

<iframe src="http://giphy.com/embed/Ibd8rxdyKlTe8" width="800" height="400" frameBorder="0" style="max-width: 100%" class="giphy-embed" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

--

### How Browserify differs from webpack

* Although Browserify doesn't offer a build tool function, It is said that difference is webpack supports to split js as multiple files.
* However substack the creator of Browserify explains here: https://gist.github.com/substack/68f8d502be42d5cd4942, it's somehow available even in Browserify.
* Browserify targets only JS (not css and images to bundle).

--

### Why webpack instead of Browserify?

* So we may say function is not so different except a build tool.
* Since Browserify is more simple tool, we eventually need the build tool.
* But I really don't want to chase build tools (grunt/gulp/brunch) anymore. (Especially gulp)
* I really agree this article(http://blog.keithcirkel.co.uk/why-we-should-stop-using-grunt/), since webpack has a function of build tool, I decide to go with `npm run`.

--

### Less configration other than grunt/gulp

https://github.com/quipper/tutor/blob/develop/webpack.config.js

`package.json`

```
  "scripts": {
    "start": "ulimit -n 10000 && ./node_modules/.bin/webpack-dev-server",
    "build": "rm -rf ./public && ./node_modules/.bin/webpack -p --config webpack.config.js",
    "test": "PHANTOMJS_BIN=\"${PHANTOMJS_BIN:=`which phantomjs`}\" ./node_modules/karma/bin/karma start karma.config.js",
    "test:chrome": "./node_modules/karma/bin/karma start karma.config.js --browsers Chrome",
    "lint": "jsxhint --harmony --jsx-only app"
  },
```

--

# Impression so far

--

### API is not so polished, and document is not so friendly

```
{ test: /\.css$/, loaders: ['style', 'css', 'autoprefixer'] }
```

```
var styles = require("style!css!./file.css");
```

--

* well, sometimes I felt excited, then get frustrated

<iframe src="http://giphy.com/embed/10kTBFEjGAkClO" width="800" height="350" frameBorder="0" style="max-width: 100%" class="giphy-embed" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

--

### Key takeaways

* When we use libraries, if the library use CoffeeScript, we have to use coffee-loader to build for the library. If library uses json file, we need json-loader. We have to be conscious to external library's stacks when we build.
* Use `require.context` for dynamic requires, see: https://github.com/webpack/docs/wiki/context
* `script-loader` can be used when library is not offered via package manager.
* `alias` is useful to rename modules.

--

### What other benefits can we have using by webpack?

<iframe src="http://giphy.com/embed/eN2cIZkymBHk4?html5=true" width="800" height="450" frameBorder="0" webkitAllowFullScreen mozallowfullscreen allowFullScreen></iframe>

--

### Some libraries start to abandon the support of bower

* Originaly bower is created solely for frontend library, but with Browserify/webpack, npm now works frontend not only for node.js library.
* Using by Browserify/webpack, we can use the library via npm and no need to fork them to build as a bower library.

--

### Jest

* Jest is new testing framework created by Facebook.
* It's Built on top of the Jasmine test framework.
* The difference is Jest replaces `require`, and mock by default, making most existing code testable.
 * Jest is based on Browserify or webpack to use it.

--

### Wrap up

* webpack's API is not so polished, but the function is ready to use on production.
* Some libraries already assumes that we use Browserify/webpack.

--

# Thank you :)
