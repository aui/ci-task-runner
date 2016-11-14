# git-webpack

[![NPM Version][npm-image]][npm-url]
[![NPM Downloads][downloads-image]][downloads-url]
[![Node.js Version][node-version-image]][node-version-url]
[![Build Status][travis-ci-image]][travis-ci-url]

与 git 绑定的 Webpack 多进程调度器，充分利用 git 与多核 CPU 加速冷编译。

* 基于 `·git commit` 按需进行构建任务调度
* 支持按模块目录、多 Webpack 实例进行构建
* 支持多线程调度 Webpack 实例
* 无需修改现有 Webpack 构建配置

## 安装

```bash
npm install git-webpack -g
```

## 使用

1\. 切换到 git 项目目录

```bash
cd you-project
```

2\. 生成配置

```bash
git-webpack --init
```

3\. 运行 git-webpack

```bash
git-webpack
```

## 配置

git-webpack.json

```javascript
{
  "modules": [],
  "watch": ["package.json"],
  "assets": "dist/assets.json",
  "parallel": 2,
  "build": {
    "force": false,
    "timeout": 60000,
    "cwd": "${moduleName}",
    "env": {
      "GIT_WEBPACK": "1"
    },
    "argv": [],
    "log": false,
    "builder": "webpack",
    "launch": "${moduleName}/webpack.config.js"
  }
}
```

路径相对于 git-webpack.json 文件。

### `modules`

设置要编译的模块目录列表。如果发生修改则会运行目录中的 webpack.config.js，`modules` 支持字符串于对象形式：

```javascript 
"modules": [
    "mod1",
    "mod2",
    {
        "name": "mod3",
        "watch": ["common/v1"],
        "build": {}
    }
]
```

### `assets`

设置构建后文件索引表输出路径。设置 `null` 则不输出。

### `watch`

`modules` 中的目录会自动进行变更监控，如果它依赖外部了文件则可以指定 `watch`,无论模块目录是否有变更，`watch` 都会触发所属模块强制编译；顶层 `watch` 变更会触发所有模块编译。

## build

构建器配置（文档尚未完善，采用默认配置可运行）。

## 最佳实践

### 持续集成

使用 CI 工具来在服务器上运行 git-webpack，前端构建、发布都将无须人工干预。

* 分支推送即自动触发构建
* 异步构建，不打断工作流
* 确保构建后的版本稳定
* 更好的权限控制

相关工具：

* gitlab: gitlab-ci
* github: travis

如果没有条件采用服务器构建，可以考虑本地 git hooks 来运行 git-webpack。

### 使用 npm scripts

* 集中管理项目所有脚本
* 管道式命令，支持串行与并行
* 智能路径，使得很多命令行工具无需全局安装

编辑 package.json，添加 npm scripts

```javascript
"scripts": {
  "build": "git-webpack --parallel 4",
  "deploy": "npm run build && npm run cdn",
  "cdn": "echo 'publish...'"
}
```

使用 npm run 启动 git-webpack

```bash
npm run build
```

本地安装 git-webpack

```bash
npm install --save-dev git-webpack
```


[npm-image]: https://img.shields.io/npm/v/git-webpack.svg
[npm-url]: https://npmjs.org/package/git-webpack
[node-version-image]: https://img.shields.io/node/v/git-webpack.svg
[node-version-url]: http://nodejs.org/download/
[downloads-image]: https://img.shields.io/npm/dm/git-webpack.svg
[downloads-url]: https://npmjs.org/package/git-webpack
[travis-ci-image]: https://travis-ci.org/aui/git-webpack.svg?branch=master
[travis-ci-url]: https://travis-ci.org/aui/git-webpack