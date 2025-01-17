# vite-plugin-rsw

> wasm-pack plugin for Vite

[![npm](https://img.shields.io/npm/v/vite-plugin-rsw.svg)](https://www.npmjs.com/package/vite-plugin-rsw)
[![npm downloads](https://img.shields.io/npm/dm/vite-plugin-rsw.svg)](https://npmjs.org/package/vite-plugin-rsw)
[![vite version](https://img.shields.io/badge/Vite-^2.0.0-000000?style=flat&labelColor=646cff)](https://github.com/vitejs/vite)
[![chat](https://img.shields.io/badge/chat-discord-blue?style=flat&logo=discord)](https://discord.gg/euyYWXTwmk)

[![awesome-rsw](./assets/awesome-rsw.svg)](https://github.com/lencx/awesome-rsw)
[![Rust](https://img.shields.io/badge/-Rust-DEA584?style=flat&logo=rust&logoColor=000000)](https://www.rust-lang.org)
[![WebAssembly](https://img.shields.io/badge/-WebAssembly-654FF0?style=flat&logo=webassembly&logoColor=ffffff)](https://webassembly.org)

|rsw version|vite version|
|---|---|
|`1.8.x`|`2.4.x`|
|`1.7.0`|`2.0.0 ~ 2.3.8`|

## Pre-installed

* [rust](https://www.rust-lang.org/learn/get-started)
* [nodejs](https://nodejs.org)
* [wasm-pack](https://github.com/rustwasm/wasm-pack)

## Article

* [中文 - WebAssembly入门](https://lencx.github.io/book/wasm/rust_wasm_frontend.html)
* [WebAssembly Series](https://github.com/lencx/fzj/discussions/22)

## Features

* startup optimization
* enable debug mode: `DEBUG=rsw yarn dev`
* friendly error message: browser and terminal
* automatically generate template when `crate` does not exist
* multiple rust crate
  * compile
  * hot-update
  * watch for dependent workspace crates ([#18](https://github.com/lencx/vite-plugin-rsw/issues/18))

![rsw run](./assets/rsw.png)
![rsw error](./assets/rsw-error.png)
![rsw debug](./assets/rsw-debug.png)

<img width="480" src="./assets/rsw-new.png" alt="rsw new">\
<img width="480" src="./assets/rsw-error-wasm-pack.png" alt="rsw error wasm-pack">\
<img width="480" src="./assets/rsw-error-outdir.png" alt="rsw error outdir">

## Plugin Options

* `cli`: specified package manager `npm` or `pnpm`, default `npm`.
* `root`: rust crate root path. default project root path.
* `unLinks`: `string[]` - (npm unlink) uninstalls a package.
* `crates`: [Item[ ]](https://github.com/lencx/vite-plugin-rsw/blob/main/src/types.ts#L26) - (npm link) package name, support npm organization.
  * *Item as string* - `'@rsw/hello'`
  * *Item as RswCrateOptions* - `{ name: '@rsw/hello', outDir: 'custom/path' }`

> **⚠️ Note:** Before performing the `vite build`, at least once `vite dev`, generate `wasm package (rust-crate/pkg)`. In the project, `wasm package` is installed by `vite-plugin-rsw` in the form of `npm link`, otherwise it will error `Can not find module 'rust-crate' or its corresponding type declarations.`

## Quick Start

[create-xc-app](https://github.com/lencx/create-xc-app): create a project in seconds!

### Step1

```bash
# With NPM
npm init xc-app

# With Yarn:
yarn create xc-app
```

You can also directly specify the project name and the template you want to use via additional command line options.

```bash
# npm 6.x
npm init xc-app my-wasm-app --template wasm-react

# npm 7+, extra double-dash is needed:
npm init xc-app my-wasm-app -- --template wasm-react

# yarn
yarn create xc-app my-wasm-app --template wasm-react
```

Currently supported template presets include `wasm-vue3` and `wasm-react`.

### Step2

```bash
cd my-wasm-app

npm install

npm run dev
```

### Step3

You can edit the `crates` in `ViteRsw` to create your own rust crates.

```js
// vite.config.ts
import { defineConfig } from 'vite';
import ViteRsw from 'vite-plugin-rsw';

export default defineConfig({
  plugins: [
    ViteRsw({
      crates: [
        '@rsw/hey', // npm org
        'rsw-test', // npm package
        // https://github.com/lencx/vite-plugin-rsw/issues/8#issuecomment-820281861
        // outDir: use `path.resolve` or relative path.
        { name: '@rsw/hello', outDir: 'custom/path' },
      ],
    }),
  ],
});
```

### Step4

Use exported Rust things from JavaScript with ECMAScript modules!

```js
import init, { greet } from '@rsw/hey';

// 1. `WebAssembly.Instance` initialization
init();

// 2. Make sure this method is executed after `init()` is called
greet('World!');
```

## Remote Deployment

```jsonc
{
  "scripts": {
+   "rsw:build": "rsw && npm run build"
  }
}
```

### Example

[lencx/learn-wasm](https://github.com/lencx/learn-wasm/blob/main/package.json)

```json
"scripts": {
  "rsw:build": "rsw && npm run build"
}
```

[.github/workflows/deploy.yml](https://github.com/lencx/learn-wasm/blob/main/.github/workflows/deploy.yml)

```yaml
# .github/workflows/deploy.yml

name: github pages

on:
  push:
    branches:
      - main

jobs:
  deploy:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v2
      - uses: actions/setup-node@v2
        with:
          node-version: '14'
      - run: yarn
+     - run: yarn rsw:build # use rsw-node build

      - name: Deploy
        uses: peaceiris/actions-gh-pages@v3
        with:
          github_token: ${{ secrets.GH_TOKEN }}
          publish_dir: ./dist
```

![rsw deploy](./assets/rsw-build.png)

## Video

* [youtube](https://youtu.be/R_lTnYaDRQ4)
* [bilibili (China)](https://www.bilibili.com/video/BV1Pw411f7pr?share_source=copy_web)

## Examples

* [learn-wasm](https://github.com/lencx/learn-wasm)
* [react](https://github.com/lencx/vite-plugin-rsw/tree/main/examples/react)
* [vue3](https://github.com/lencx/vite-plugin-rsw/tree/main/examples/vue3)

## Credits

### [Backers](https://opencollective.com/vite-plugin-rsw)

Thank you to all our backers! 🙏

<a href="https://opencollective.com/vite-plugin-rsw" target="_blank"><img src="https://opencollective.com/vite-plugin-rsw/tiers/backer.svg?avatarHeight=36&width=600"></a>

## 微信

> 群二维码已过期，关注公众号《浮之静》，发送“进群”，我将拉你进群一起学习。

<img height="180" src="./assets/wasm-qrcode.png" alt="wasm-wechat-qrcode" /> <img height="160" src="./assets/fzj-qrcode.png" alt="fzj-qrcode" />

## License

MIT License © 2021 [lencx](https://github.com/lencx)
