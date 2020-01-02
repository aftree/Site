---
title: "关于"
date: 2019-12-15T20:50:40+08:00
draft: true
#pre: "<b>1. </b>"
weight: 100
---

https://www.gohugo.org/

### 运行Hugo

在你的站点根目录执行 `Hugo` 命令进行调试：

```bash
$ hugo server --theme=hyde --buildDrafts
```

（注明：v0.15 版本之后，不再需要使用 `--watch` 参数了）

浏览器里打开： `http://localhost:1313`

### 部署

假设你需要部署在 `GitHub Pages` 上，首先在GitHub上创建一个Repository，命名为：`coderzh.github.io` （coderzh替换为你的github用户名）。

在站点根目录执行 `Hugo` 命令生成最终页面：

```bash
$ hugo --theme=hugo --baseUrl="https://lijun.in/"
```

（注意，以上命令并不会生成草稿页面，如果未生成任何文章，请去掉文章头部的 `draft=true` 再重新生成。）

如果一切顺利，所有静态页面都会生成到 `public` 目录，将pubilc目录里所有文件 `push` 到刚创建的Repository的 `master` 分支。