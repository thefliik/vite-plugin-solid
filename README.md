# ⚡ vite-plugin-solid

A simple integration to run [solid-js](https://github.com/ryansolid/solid) with [vite](https://github.com/vitejs/vite)

<img alt="HMR gif demonstrationdemodemodemo" src=".github/hmr.gif">

# Got a question? / Need help?

Join [solid discord](https://discord.com/invite/solidjs) and check the [troubleshooting section](#troubleshooting) to see if your question hasn't been already answered.

## Features

- HMR with no configuration needed
- Drop-in installation as a vite plugin
- Minimal bundle size
- Support typescript (`.tsx`) out of the box
- Support code splitting out of the box

## Requirements

We've recently made this module 100% esm compatible. As per [this document](https://nodejs.org/api/esm.html) it is strongly recommended to have at least version `14.13.0` of node installed.

You can check your current version of node by typing `node -v` in your terminal. If your version is below that one version I'd encourage you to either do an update globally or use a node version management tool such as [volta](https://volta.sh/) or [nvm](https://github.com/nvm-sh/nvm).

## Quickstart

You can use the [vite-template-solid](https://github.com/amoutonbrady/vite-template-solid) starter templates similar to CRA:

```bash
$ npx degit amoutonbrady/vite-template-solid/js my-project
$ cd my-project
$ npm install # or pnpm install or yarn install
$ npm run start # starts dev-server with hot-module-reloading
$ npm run build # builds to /dist
```

## Installation

Install `vite`, `vite-plugin-solid` and `babel-preset-solid` as dev dependencies.

Install `solid-js` as dependency.

You have to install those so that you are in control to which solid version is used to compile your code.

```bash
# with npm
$ npm install -D vite vite-plugin-solid babel-preset-solid
$ npm install solid-js

# with pnpm
$ pnpm add -D vite vite-plugin-solid babel-preset-solid
$ pnpm add solid-js

# with yarn
$ yarn add -D vite vite-plugin-solid babel-preset-solid
$ yarn add solid-js
```

Add it as plugin to `vite.config.js`

```js
// vite.config.ts
import { defineConfig } from 'vite';
import { solidPlugin } from 'vite-plugin-solid';

export default defineConfig({
  plugins: [solidPlugin()],
});
```

## Run

Just use regular `vite` or `vite build` commands

```json
{
  "scripts": {
    "dev": "vite",
    "build": "vite build"
  }
}
```

## API

### options

- Type: Object
- Default: {}

#### options.dev

- Type: Boolean
- Default: true

This will inject `solid-js/dev` in place of `solid-js` in dev mode. Has no effect in prod.
If set to false, it won't inject it in dev.
This is useful for extra logs and debug.

#### options.hmr

- Type: Boolean
- Default: true

This will inject HMR runtime in dev mode. Has no effect in prod.
If set to false, it won't inject the runtime in dev.

#### options.ssr

- Type: Boolean
- Default: false

This will force SSR code in the produced files. This is experiemental and mostly not working yet.

#### options.babel

- Type: Babel.TransformOptions
- Default: {}

Pass any additional [babel transform options](https://babeljs.io/docs/en/options). Those will be merged with the transformations required by Solid.

## Note on HMR

Starting from version `1.1.0`, this plugin handle automatic HMR via [solid-refresh](https://github.com/ryansolid/solid-refresh).

At this stage it's still early work but provide basic HMR. In order to get the best out of it there are couple of things to keep in mind:

- Every component that you expect to have HMR should be exported as default eg:

```tsx
const MyComponent = () => <h1>My component</h1>;
export default MyComponent;
```

- When you modify a file every state below this component will be reset to default state (including the current file). The state in parent component is preserved.

- The entrypoint can't benefit from HMR yet and will force a hard reload of the entire app. This is still really fast thanks to browser caching.

- Currently there's a parsing issue if you export default on the same line as declaration like so: `export default function App() {...}`. It should be made in two steps. First declare then export, similar to the example in the first bullet point.

If at least one of this point is blocking to you, you can revert to the old behavior but [opting out the automatic HMR](#options) and placing the following snippet in your entry point:

```jsx
const dispose = render(() => <App />, document.body);

if (import.meta.hot) {
  import.meta.hmr.accept();
  import.meta.hmr.dispose(dispose);
}
```

# Troubleshooting

- It appears that Webstorm generate some weird triggers when saving a file. In order to prevent that you can follow [this thread](https://intellij-support.jetbrains.com/hc/en-us/community/posts/360000154544-I-m-having-a-huge-problem-with-Webstorm-and-react-hot-loader-) and disable the **"Safe Write"** option in **"Settings | Appearance & Behavior | System Settings"**.

- If one of your dependency spit out React code instead of Solid that means that they don't expose JSX properly. To get around it, you might want to manually exclude it from the [dependecies optimization](https://vitejs.dev/config/#optimizedeps-exclude)

## Migration from v1

The master branch now target vite 2.

The main breaking change from previous version is that the package has been renamed from `@amoutonbrady/vite-plugin-solid` to `vite-plugin-solid`.

For other breaking changes, check [the migration guide of vite](https://vitejs.dev/guide/migration.html).

# Credits

- [solid-js](https://github.com/ryansolid/solid) and [vite](https://github.com/vitejs/vite#readme) obviously
- [vite](https://github.com/vitejs/vite) obviously
