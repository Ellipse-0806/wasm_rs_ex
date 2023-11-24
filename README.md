# wasm_rs_ex

## About

WebAssembly with Rust Example.

## tools

```bash
cargo install wasm-pack
```

## build

### web

```bash
wasm-pack build --target web
```

## node environment setup

1. install nvm
```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.5/install.sh | bash
source ~/.profile
command -v nvm
```

2. install node latest
```bash
nvm ls-remote
# v20.10.0   (Latest LTS: Iron)
nvm install <version>
# nvm install 20.10.0
node -v
# v20.10.0
```

3. install npm (latest supported)
```bash
nvm install-latest-npm
npm -v
# 10.2.4
npm install npm@latest -g # check install npm latest version
```

4. check install version and select used version
```bash
nvm list
# ->     v20.10.0
# default -> 20.10.0 (-> v20.10.0)
# iojs -> N/A (default)
# unstable -> N/A (default)
# node -> stable (-> v20.10.0) (default)
# stable -> 20.10 (-> v20.10.0) (default)
# ...
nvm use <version>
# nvm use 20.10.0
# # Now using node v20.10.0 (npm v10.2.4)
```

## Running on webpack

1. Install wasm project template
```bash
npm init wasm-app www
```

2. Build project to bundler
```bash
wasm-pack build --target bundler
```

## 99. Trouble-shooting

1. Error `npm install`

```
npm ERR! code ERESOLVE
npm ERR! ERESOLVE unable to resolve dependency tree
npm ERR! 
npm ERR! While resolving: create-wasm-app@0.1.0
npm ERR! Found: webpack@5.89.0
npm ERR! node_modules/webpack
npm ERR!   dev webpack@"^5.89.0" from the root project
npm ERR! 
npm ERR! Could not resolve dependency:
npm ERR! peer webpack@"4.x.x" from webpack-cli@3.3.12
npm ERR! node_modules/webpack-cli
npm ERR!   dev webpack-cli@"^3.1.0" from the root project
npm ERR! 
npm ERR! Fix the upstream dependency conflict, or retry
npm ERR! this command with --force or --legacy-peer-deps
npm ERR! to accept an incorrect (and potentially broken) dependency resolution.
```

- Fix dependencies package version (www/package.json)

```
  "devDependencies": {
    "copy-webpack-plugin": "^5.0.0",
    "hello-wasm-pack": "^0.1.0",
    "@wasm-tool/wasm-pack-plugin": "1.5.0",
    "html-webpack-plugin": "^5.3.2",
    "text-encoding": "^0.7.0",
    "webpack": "^5.49.0",
    "webpack-cli": "^4.7.2",
    "webpack-dev-server": "^4.15.1"
  }
```

1. Error `npm run start`
```
ERROR in ../pkg/hello_wasm_bg.wasm 1:0
Module parse failed: Unexpected character '' (1:0)
The module seem to be a WebAssembly module, but module is not flagged as WebAssembly module for webpack.
BREAKING CHANGE: Since webpack 5 WebAssembly is not enabled by default and flagged as experimental feature.
You need to enable one of the WebAssembly experiments via 'experiments.asyncWebAssembly: true' (based on async modules) or 'experiments.syncWebAssembly: true' (like webpack 4, deprecated).
For files that transpile to WebAssembly, make sure to set the module type in the 'module.rules' section of the config (e. g. 'type: "webassembly/async"').
(Source code omitted for this binary file)
 @ ../pkg/hello_wasm.js 1:0-45 3:15-19
 @ ./index.js 1:0-35 3:0-10
 @ ./bootstrap.js 4:0-20
```

- Add module.export in `webpack.config.js`

```js
const CopyWebpackPlugin = require("copy-webpack-plugin");
const path = require('path');

module.exports = {
  entry: "./bootstrap.js",
  output: {
    path: path.resolve(__dirname, "dist"),
    filename: "bootstrap.js",
  },
  experiments: {                // Add parameter
    asyncWebAssembly: true,     //
  },                            //
  mode: "development",
  plugins: [
    new CopyWebpackPlugin(['index.html'])
  ],
};
```