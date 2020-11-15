---
title: Hexo的Plugin與使用
tags: [Network,Server,Git,Blog,Wiki]
---

## Plugin安裝方法

Hexo Plugin官方網頁:[https://hexo.io/plugins/](https://hexo.io/plugins/)

基本命令

```bash
#安裝
npm install <plugin> --save
#解除安裝
npm uninstall <plugin> --save
#更新plugin & Framework(under Hexo Dir)
npm update
```

## 本站有安裝的Plugin

* [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)
* [hexo-renderer-markdown-it-plus](https://github.com/CHENXCHEN/hexo-renderer-markdown-it-plus)

## 相關設定

#### Render-markdown-it-plus Section

```c
markdown_it_plus:
    highlight: true
    html: true
    xhtmlOut: true
    breaks: true
    langPrefix:
    linkify: true
    typographer:
    quotes: “”‘’
    pre_class: highlight
```

#### Deployment Section

```c
deploy:
  type: git
  repo: https://github.com/<Username>/<username>.github.io
  branch: <branch_name>
```

