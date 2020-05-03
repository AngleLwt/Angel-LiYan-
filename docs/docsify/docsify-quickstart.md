# 快速上手 docsify

> 本文由 [简悦 SimpRead](http://ksria.com/simpread/) 转码， 原文地址 https://docsify.js.org/#/zh-cn/quickstart

推荐安装 `docsify-cli` 工具，可以方便创建及本地预览文档网站。

```
npm i docsify-cli -g
```

## 初始化项目

如果想在项目的 `./docs` 目录里写文档，直接通过 `init` 初始化项目。

```
docsify init ./docs
```

## 开始写文档

初始化成功后，可以看到 `./docs` 目录下创建的几个文件

*   `index.html` 入口文件
*   `README.md` 会做为主页内容渲染
*   `.nojekyll` 用于阻止 GitHub Pages 会忽略掉下划线开头的文件

直接编辑 `docs/README.md` 就能更新网站内容，当然也可以写多个页面。

## 本地预览网站

运行一个本地服务器通过 `docsify serve` 可以方便的预览效果，而且提供 LiveReload 功能，可以让实时的预览。默认访问 [http://localhost:3000](http://localhost:3000/) 。

```
docsify serve docs
```

更多命令行工具用法，参考 [docsify-cli 文档](https://github.com/docsifyjs/docsify-cli)。

## 手动初始化

如果不喜欢 npm 或者觉得安装工具太麻烦，我们其实只需要直接创建一个 `index.html` 文件。

_index.html_

```
<!DOCTYPE html>
<html>
<head>
  <meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
  <meta >
  <meta charset="UTF-8">
  <link rel="stylesheet" href="//unpkg.com/docsify/themes/vue.css">
</head>
<body>
  <div id="app"></div>
  <script>
    window.$docsify = {
      //...
    }
  </script>
  <script src="//unpkg.com/docsify/lib/docsify.min.js"></script>
</body>
</html>
```

如果系统里安装 Python 的话，也可以很轻易的启动一个静态服务器。

```
cd docs && python -m SimpleHTTPServer 3000
```

## Loading 提示

初始化时会显示 `Loading...` 内容，你可以自定义提示信息。

_index.html_

```
<div id="app">加载中</div>
```

如果更改了 `el` 的配置，需要将该元素加上 `data-app` 属性。

_index.html_

```
<div data-app id="main">加载中</div>

  <script>
    window.$docsify = {
      el: '#main'
    }
  </script>
```
