# Rollup-all

[![Build Status](https://travis-ci.org/harrison-ifeanyichukwu/rollup-all.svg?branch=master)](https://travis-ci.org/harrison-ifeanyichukwu/rollup-all)
[![Coverage Status](https://coveralls.io/repos/github/harrison-ifeanyichukwu/rollup-all/badge.svg?branch=master)](https://coveralls.io/github/harrison-ifeanyichukwu/rollup-all?branch=master)
[![semantic-release](https://img.shields.io/badge/%20%20%F0%9F%93%A6%F0%9F%9A%80-semantic--release-e10079.svg)](https://github.com/semantic-release/semantic-release)
[![npm version](https://badge.fury.io/js/rollup-all.svg)](https://badge.fury.io/js/rollup-all)
![npm](https://img.shields.io/npm/dt/rollup-all.svg)

## Overview

Rollup-all is a lightweight, extensive and configurable npm package for building all your shiny JavaScript/Typescript Library source codes in one parse using [rollup.js](https://rollupjs.org/guide/en).

It enables you to generate separate project [lib and dist builds](https://stackoverflow.com/questions/39553079/difference-between-lib-and-dist-folders-when-packaging-library-using-webpack) using any of the supported [rollup.js build formats](https://rollupjs.org/guide/en#big-list-of-options) including support for minification.

It allows you to configure the build process, letting you define what should be included and excluded in the build, if sourcemap should be generated, if minified versions of the build should be generated, if **asset and type definition files** should be copied over, and lots more...

## What's New

The latest version of this project can copy asset and typescript type definition files to any deep length.

The latest version also checks for `process.env.NODE_ENV`, and will minify build if running in production environment, ignoring config settings as long as you provided a minifier plugin.

**Note that minified files no longer has .min extension attached, that is to say, there is no separate file created for a minified build anymore. We decided to just generate one output file, to keep things consistent.**

## Getting Started

```bash
npm install --save-dev rollup-all
```

The api call is like shown below:

```typescript
rollupAll.getExports(uglifierPlugin: object | null, otherPlugins: object[], config: string | UserConfig);
```

## Setup Sample for Typescript, Jest and Babel 7

1. **Install Dependencies & Dev Dependencies**

    **install Rollup and rollup-all:**

    ```bash
    npm install --save-dev rollup rollup-plugin-babel rollup-plugin-commonjs rollup-plugin-node-resolve rollup-plugin-uglify rollup-all
    ```

    **install babel 7:**

    ```bash
    npm install --save-dev @babel/core @babel/preset-env @babel/plugin-proposal-class-properties @babel/plugin-proposal-object-rest-spread
    ```

    **install Typescript & Typescript babel plugin:**

    ```bash
    npm install --save-dev typescript @babel/preset-typescript
    ```

    **install Jest & Babel-Jest:**

    ```bash
    npm install --save-dev jest @types/jest babel-jest
    ```

    **Install babel runtime helpers:**

    ```bash
    npm install --save @babel/runtime @babel/plugin-transform-runtime
    ```

    this is optional if you want to use **ES2017^** features such as `async-await`

2. **Create a `rollup.js` file in your root directory containing the following sample code:**

    ```javascript
    import resolve from 'rollup-plugin-node-resolve';
    import commonjs from 'rollup-plugin-commonjs';
    import babel from 'rollup-plugin-babel';
    import { uglify } from 'rollup-plugin-uglify';

    import RollupAll from 'rollup-all';

    const plugins = [
        resolve({
            extensions: ['.ts', '.js'],
        }),
        commonjs({
            include: 'node_modules/**'
        }),
        babel({
            exclude: 'node_modules/**',
            extensions: ['.ts', '.js'],
            runtimeHelpers: true // use this option if your code makes use of es2017 async-await
        }),
    ];

    export default RollupAll.getExports(uglify(), plugins);
    ```

    > **Note**: if you are not using any [uglifier](https://github.com/rollup/rollup/wiki/Plugins#output--prettifying) plugin, pass in `null` as the first parameter to **getExports**.

3. **Create a `.babelrc` file in your root directory containing the following sample code:**

    ```json
    {
        "env": {
            "test": {
                "presets": [
                    [
                        "@babel/preset-env",
                    ],
                    "@babel/preset-typescript",
                ],
                "plugins": [
                    "@babel/plugin-transform-runtime",
                    "@babel/proposal-class-properties",
                    "@babel/proposal-object-rest-spread"
                ]
            },
            "build": {
                "presets": [
                    [
                        "@babel/env",
                        {
                            "modules": false,
                        }
                    ],
                    "@babel/preset-typescript",
                ],
                "plugins": [
                    "@babel/plugin-transform-runtime",
                    "@babel/proposal-class-properties",
                    "@babel/proposal-object-rest-spread"
                ]
            }
        }
    }
    ```

4. **Add a `tsconfig.json` file to your root directory containing the following sample code:**

    ```json
    {
        "compilerOptions": {
            "moduleResolution": "node",
            "target": "esnext",
            "declaration": true,
            "emitDeclarationOnly": true,
            "declarationDir": "./typings",
            "noImplicitAny": false,
            "downlevelIteration": true,
            "strictNullChecks": true,
            "lib": [
                "dom",
                "es5",
                "es2015.collection",
                "es2015.iterable"
            ]
        },
        "include": [
            "src"
        ]
    }
    ```

5. **Setup jest for testing, add a jest.config.js file with the following sample code:**

    ```js
    module.exports = {
        testEnvironment: 'node',
        collectCoverage: true,
        transform: {
            '^.+\\.tsx?$': 'babel-jest'
        },
        testRegex: '\\.spec\\.ts',
    };
    ```

6. **Add relevant scripts and properties to your package.json file:**

    ```json
    {
        "name": "project-name",
        "version": "1.0.0",
        "description": "project description",
        "main": "lib/index",
        "typings": "lib/typings/index",
        "scripts": {
            "test": "BABEL_ENV=test jest",
            "watch-test": "BABEL_ENV=test jest --watch",
            "build": "tsc && BABEL_ENV=build rollup --config",
            "lint": "eslint ./src --fix"
        },
        "devDependencies": {
            "@babel/core": "7.4.0",
            "@babel/plugin-proposal-class-properties": "^7.4.0",
            "@babel/plugin-proposal-object-rest-spread": "^7.4.0",
            "@babel/preset-env": "7.4.0",
            "@babel/preset-typescript": "7.3.3",
            "@types/jest": "24.0.11",
            "@typescript-eslint/eslint-plugin": "1.6.0",
            "@typescript-eslint/parser": "1.6.0",
            "babel-jest": "24.3.1",
            "jest": "24.3.1",
            "rollup": "0.66.6",
            "rollup-plugin-babel": "4.3.2",
            "rollup-plugin-commonjs": "9.3.4",
            "rollup-plugin-node-resolve": "3.4.0",
            "rollup-plugin-uglify": "6.0.2",
            "typescript": "3.4.5"
        },
        "dependencies": {
            "@babel/plugin-transform-runtime": "7.4.3",
            "@babel/runtime": "7.4.3"
        }
    }
    ```

7. **Define your optional .buildrc.js config file for customizing your build:**

    ```javascript
    module.exports = {
        /**
         * defines code src directory
         */
        srcDir: 'src',

        mainModuleFileName: 'index.ts',

        mainModuleName: 'ModuleName',

        /**
         * boolean value indicating if files that are not part of the listed allowed
         * fileExtensions and are not type definition files should be copied over
         */
        copyAssets: true,

        /**
         * boolean indicating if sourcemap should be generated, can be true, false, or 'inline'
         */
        sourcemap: true,

        /**
         * defines config settings for generating distributed codes. such as browser iife outputs
         */
        distConfig: {
            enabled: false
        },

        /**
         * defines config settings for generating lib codes. output format is cjs.
         */
        libConfig: {
            enabled: true
        }
    }
    ```

8. **Ready to go, start creating, testing and building your project.**

## How It Works

Rollup-all uses an internal `.buildrc.js` file that defines default build configurations for your project. the full config options is as shown below:

```typescript
{

    srcDir: 'src',

    mainModuleFileName: 'index.js',

    mainModuleName: 'Module',

    /**
     * allowed file extensions
     */
    fileExtensions: ['.js', '.ts'],

    /**
     * defines string of file patterns to process
     */
    include: ['*'],

    /**
     * defines string of file patterns to ignore. by default, type definition files are ignore
     */
    exclude: [],

    /**
     * boolean value indicating if files that are not part of the listed allowed
     * fileExtensions and are not type definition files should be copied over
     */
    copyAssets: true,

    /**
     * boolean indicating if generated output files should be uglified, you must
     * pass in an uglifier plugin if set to true
     */
    uglify: false,

    /**
     * boolean indicating if the interop rollup setting should be enabled
     */
    interop: false,

    /**
     * boolean indicating if sourcemap should be generated, can be true, false, or 'inline'
     */
    sourcemap: true,

    /**
     * rollup watch config, you must pass in the --watch command line argument for this to
     * work
     */
    watch: {},

    /**
     * rollup globals config
     */
    globals: {},

    /**
     * defines config settings for generating distributed codes. such as browser iife outputs
     */
    distConfig: {
        /**
         * defines if dist is enabled
         */
        enabled: false,

        /**
         * defines output directory
         */
        outDir: 'dist',

        format: 'iife',

        /**
         * boolean value indicating if typescript type definition files should be copied to the
         * specified typings directory
         */
        copyTypings: false,

        /**
         * defines folder to copy all type definition files to, if the copy typings option
         * is set to true
         */
        typingsDir: 'dist/typings',
    },

    /**
     * defines config settings for generating lib codes. output format is cjs.
     */
    libConfig: {

        /**
         * defines if lib is enabled
         */
        enabled: true,

        /**
         * defines output directory
         */
        outDir: 'lib',

        format: 'cjs',

        /**
         * boolean value indicating if typescript type definition files should be copied to the
         * specified typings directory
         */
        copyTypings: true,

        /**
         * defines folder to copy all type definition files to, if the copy typings option
         * is set to true
         */
        typingsDir: 'lib/typings',
    }
}
```

You can override these options by creating and placing a `.buildrc.js` file in your project's root directory. You can even name it any other thing or place it anywhere provided you supply the file's relative/absolute path as a third parameter during the [api call](#getting-started).

### Config Options Explained

> Options with ending asterisks can be defined locally on the `distConfig` and `libConfig` sections.

- **srcDir**: defines the relative path to your project's source folder. Defaults to `src`.

- **mainModuleFileName**: defines the projects main file name such as `index.js`, `main.js`, `entry.js`. Defaults to `index.js`.

- **mainModuleName**: defines the globally exposed module name. this applies specifically to projects that uses `iife` dist builds. e.g, `JQuery` for jquery, `React` for react,

- **fileExtensions**: defines an array of project src code file extensions to process. Every other file within the src directory is regarded as asset files, except **.d.ts** files which are regarded as type definition files.

- **exclude**: defines a list of modules to exclude from the build. Entries can be strings or regex. e.g `"src/modules/*.js"` ignores every .js file inside src/modules directory.

- **include**: defines a list of modules to include in the build. Entries can be strings or regex. Defaults to `["*"]`, which includes everything.

- **copyAssets**: defines if asset files should be copied over during the build process.

- **copyTypings**: defines if type definition files should be copied over during the build process. Defaults to true for lib build, but false for dist build.

- **typingsDir**: defines directory to copy type definition files to. defaults to **lib/typings** for lib build, and **dist/typings** for dist build. The typings directory should match with the entry in your **tsconfig.json** file.

- **uglify***: defines if the build should be minified. **Note that when running in production environment, this value is automatically set to true**. You must supply an uglifier plugin for it to work. Default value is `false`.

- **interop**: defines if `rollup.js` interop functionality should be enabled.

- **sourcemap**: defines if the build should produce source maps. value can be `true`, `"inline"` or `false`. Default value is `true`.

- **globals**: as per [rollup.js](https://rollupjs.org/guide/en#output-globals-g-globals) docs, this is an object of `id: name` pairs, used for `umd/iife` builds. Defaults to empty object `{}`

- **watch**: as per [rollup.js](https://rollupjs.org/guide/en#watch-options) docs, this option only takes effect when running Rollup with the --watch flag.

- **outDir** - defines build output directory.

- **enabled** - defines if a specific build type is enabled. By default, **Node.JS** lib build is enabled, while browser based dist build is disabled.

## Contributing

We welcome your own contributions, ranging from code refactoring, documentation improvements, new feature implementations, bugs/issues reporting, etc. **Thanks in advance!!!**
