---
title: npm 发布前转换工具
date: 2020-09-23 10:39:17
tags: npm
---

# 背景

事业部需要自己的 UI 风格，多个项目需要使用同一套代码风格。
发布 npm 以供各个项目使用。本文着重讲工具

# 需求

- ts 代码转换成 es5 形式
- less 代码转成普通 css
- 压缩图片
- 替换引入 less 代码为引入 css

# 依赖包

- gulp
- gulp-less
- gulp-typescript
- gulp-imagemin
- gulp-replace

# 准备

- npm 账号

# 目录结构

![menu](menu.png)

# 步骤

### **开发代码**

src/components 为需要发布的代码，其中有 index.ts/index.js 引入各个 components。发布后入口文件为此 index.ts/index.js

### \*\*添加 tsconfig.json 到 components 文件夹

转码 ts 文件规则，文件见结尾

### **更改 package.json**

添加如下代码，例子见结尾

```
  "name": "xxx",              //npm库名字
  "version": "0.0.9",         //版本号
  "main": "lib/index.js",     //npm库入口
  "files": [                  //发布文件
    "lib"
  ],
```

### **添加 gulpfile.js**

转码 src/components 文件夹中的 ts/js/css 代码到 lib 文件夹中，代码文件见结尾

### 执行命令

```
gulp
```

```
npm publish
```

# components/tsconfig.json

```
{
  "compilerOptions": {
    /* Basic Options */
    "baseUrl": "./",
    "outDir": "./../../lib",
    "jsx": "react",
    "target": "es5" /* Specify ECMAScript target version: 'ES3' (default), 'ES5', 'ES2015', 'ES2016', 'ES2017','ES2018' or 'ESNEXT'. */,
    "module": "commonjs" /* Specify module code generation: 'none', 'commonjs', 'amd', 'system', 'umd', 'es2015', or 'ESNext'. */,
    // "lib": [],                             /* Specify library files to be included in the compilation. */
    // "allowJs": true,                       /* Allow javascript files to be compiled. */
    // "checkJs": true,                       /* Report errors in .js files. */
    // "jsx": "preserve",                     /* Specify JSX code generation: 'preserve', 'react-native', or 'react'. */
    // "declaration": true,                   /* Generates corresponding '.d.ts' file. */
    // "declarationMap": true,                /* Generates a sourcemap for each corresponding '.d.ts' file. */
    // "sourceMap": true,                     /* Generates corresponding '.map' file. */
    // "outFile": "./",                       /* Concatenate and emit output to single file. */
    // "outDir": "./",                        /* Redirect output structure to the directory. */
    // "rootDir": "./",                       /* Specify the root directory of input files. Use to control the output directory structure with --outDir. */
    // "composite": true,                     /* Enable project compilation */
    // "removeComments": true,                /* Do not emit comments to output. */
    // "noEmit": true,                        /* Do not emit outputs. */
    // "importHelpers": true,                 /* Import emit helpers from 'tslib'. */
    // "downlevelIteration": true,            /* Provide full support for iterables in 'for-of', spread, and destructuring when targeting 'ES5' or 'ES3'. */
    // "isolatedModules": true,               /* Transpile each file as a separate module (similar to 'ts.transpileModule'). */

    /* Strict Type-Checking Options */
    "strict": false /* Enable all strict type-checking options. */,
    // "noImplicitAny": true,                 /* Raise error on expressions and declarations with an implied 'any' type. */
    // "strictNullChecks": true,              /* Enable strict null checks. */
    // "strictFunctionTypes": true,           /* Enable strict checking of function types. */
    // "strictBindCallApply": true,           /* Enable strict 'bind', 'call', and 'apply' methods on functions. */
    // "strictPropertyInitialization": true,  /* Enable strict checking of property initialization in classes. */
    // "noImplicitThis": true,                /* Raise error on 'this' expressions with an implied 'any' type. */
    // "alwaysStrict": true,                  /* Parse in strict mode and emit "use strict" for each source file. */

    /* Additional Checks */
    // "noUnusedLocals": true,                /* Report errors on unused locals. */
    // "noUnusedParameters": true,            /* Report errors on unused parameters. */
    // "noImplicitReturns": true,             /* Report error when not all code paths in function return a value. */
    // "noFallthroughCasesInSwitch": true,    /* Report errors for fallthrough cases in switch statement. */

    /* Module Resolution Options */
    // "moduleResolution": "node",            /* Specify module resolution strategy: 'node' (Node.js) or 'classic' (TypeScript pre-1.6). */
    // "baseUrl": "./",                       /* Base directory to resolve non-absolute module names. */
    // "paths": {},                           /* A series of entries which re-map imports to lookup locations relative to the 'baseUrl'. */
    // "rootDirs": [],                        /* List of root folders whose combined content represents the structure of the project at runtime. */
    // "typeRoots": [],                       /* List of folders to include type definitions from. */
    // "types": [],                           /* Type declaration files to be included in compilation. */
    // "allowSyntheticDefaultImports": true,  /* Allow default imports from modules with no default export. This does not affect code emit, just typechecking. */
    "esModuleInterop": true /* Enables emit interoperability between CommonJS and ES Modules via creation of namespace objects for all imports. Implies 'allowSyntheticDefaultImports'. */
    // "preserveSymlinks": true,              /* Do not resolve the real path of symlinks. */

    /* Source Map Options */
    // "sourceRoot": "",                      /* Specify the location where debugger should locate TypeScript files instead of source locations. */
    // "mapRoot": "",                         /* Specify the location where debugger should locate map files instead of generated locations. */
    // "inlineSourceMap": true,               /* Emit a single file with source maps instead of having a separate file. */
    // "inlineSources": true,                 /* Emit the source alongside the sourcemaps within a single file; requires '--inlineSourceMap' or '--sourceMap' to be set. */

    /* Experimental Options */
    // "experimentalDecorators": true,        /* Enables experimental support for ES7 decorators. */
    // "emitDecoratorMetadata": true,         /* Enables experimental support for emitting type metadata for decorators. */
  }
}

```

# gulpfile.js

```
const gulp = require("gulp");
const gulpless = require("gulp-less");
const ts = require("gulp-typescript");
const tsProject = ts.createProject("src/components/tsconfig.json");
var imageMin = require("gulp-imagemin");
var replace = require("gulp-replace");
var del = require("del");
let output = "lib";
async function clean(cb) {
  await del(["lib"], cb);
  cb();
}
function less(cb) {
  gulp
    .src(["src/components/**/*.less", "src/components/**/*.css"], {
      allowEmpty: true,
    })
    .pipe(gulpless())
    .pipe(gulp.dest(output));
  cb();
}

function config(cb) {
  // gulp.src(["src/components/*.md"]).pipe(gulp.dest(output));
  // gulp.src("package.json").pipe(gulp.dest(output));
  cb();
}
function image(cb) {
  gulp
    .src(
      [
        "src/components/**/*.png",
        "src/components/**/*.jpg",
        "src/components/**/*.svg",
      ],
      {
        sourcemaps: true,
        // allowEmpty: true,
      }
    )
    .pipe(imageMin({ progressive: true }))
    .pipe(gulp.dest(output, { sourcemaps: true }));
  cb();
}
function js(cb) {
  tsProject
    .src()
    .pipe(replace(".less", ".css"))
    .pipe(tsProject())
    .js.pipe(gulp.dest(output));
  cb();
}
exports.republish = gulp.series(clean, gulp.parallel(less, config, image, js));


```

# package.json

```
{
  "name": "xxx",
  "version": "0.0.9",
  "main": "lib/index.js",
  "files": [
    "lib"
  ],
  "dependencies": {
    "@svgr/webpack": "4.3.3",
    "@testing-library/jest-dom": "^4.2.4",
    "@testing-library/react": "^9.3.2",
    "@testing-library/user-event": "^7.1.2",
    "@types/jest": "^24.0.0",
    "@types/node": "^12.0.0",
    "@types/react": "^16.9.0",
    "@types/react-dom": "^16.9.0",
    "@types/react-router-dom": "^5.1.5",
    "@typescript-eslint/eslint-plugin": "^2.10.0",
    "@typescript-eslint/parser": "^2.10.0",
    "antd-mobile": "^2.3.3",
    "babel-eslint": "10.1.0",
    "babel-jest": "^24.9.0",
    "babel-loader": "8.1.0",
    "babel-plugin-named-asset-import": "^0.3.6",
    "babel-preset-react-app": "^9.1.2",
    "camelcase": "^5.3.1",
    "case-sensitive-paths-webpack-plugin": "2.3.0",
    "css-loader": "3.4.2",
    "dotenv": "8.2.0",
    "dotenv-expand": "5.1.0",
    "eslint": "^6.6.0",
    "eslint-config-react-app": "^5.2.1",
    "eslint-loader": "3.0.3",
    "eslint-plugin-flowtype": "4.6.0",
    "eslint-plugin-import": "2.20.1",
    "eslint-plugin-jsx-a11y": "6.2.3",
    "eslint-plugin-react": "7.19.0",
    "eslint-plugin-react-hooks": "^1.6.1",
    "file-loader": "4.3.0",
    "fs-extra": "^8.1.0",
    "html-webpack-plugin": "4.0.0-beta.11",
    "identity-obj-proxy": "3.0.0",
    "jest": "24.9.0",
    "jest-environment-jsdom-fourteen": "1.0.1",
    "jest-resolve": "24.9.0",
    "jest-watch-typeahead": "0.4.2",
    "less": "^3.12.2",
    "less-loader": "^6.2.0",
    "mini-css-extract-plugin": "0.9.0",
    "optimize-css-assets-webpack-plugin": "5.0.3",
    "pnp-webpack-plugin": "1.6.4",
    "postcss-flexbugs-fixes": "4.1.0",
    "postcss-loader": "3.0.0",
    "postcss-normalize": "8.0.1",
    "postcss-preset-env": "6.7.0",
    "postcss-safe-parser": "4.0.1",
    "react": "^16.13.1",
    "react-app-polyfill": "^1.0.6",
    "react-dev-utils": "^10.2.1",
    "react-dom": "^16.13.1",
    "react-router-dom": "^5.2.0",
    "react-transition-group": "^4.4.1",
    "resolve": "1.15.0",
    "resolve-url-loader": "3.1.1",
    "sass-loader": "8.0.2",
    "semver": "6.3.0",
    "style-loader": "0.23.1",
    "terser-webpack-plugin": "2.3.5",
    "ts-pnp": "1.1.6",
    "typescript": "~3.7.2",
    "url-loader": "2.3.0",
    "webpack": "4.42.0",
    "webpack-dev-server": "3.10.3",
    "webpack-manifest-plugin": "2.2.0",
    "workbox-webpack-plugin": "4.3.1"
  },
  "scripts": {
    "start": "node scripts/start.js",
    "build": "node scripts/build.js",
    "test": "node scripts/test.js",
    "republish": "gulp republish"
  },
  "babel": {
    "presets": [
      "react-app"
    ]
  },
  "devDependencies": {
    "@babel/core": "^7.9.0",
    "del": "^5.1.0",
    "gulp": "^4.0.2",
    "gulp-imagemin": "^7.1.0",
    "gulp-less": "^4.0.1",
    "gulp-replace": "^1.0.0",
    "gulp-sequence": "^1.0.0",
    "gulp-typescript": "^6.0.0-alpha.1"
  }
}
```
