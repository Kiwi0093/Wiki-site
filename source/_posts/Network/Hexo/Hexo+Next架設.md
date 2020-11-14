---
title: 透過Hexo在Github上架設Blog
date: 2020-11-14
tags: [Network, server, Blog]
---

## 事前準備

* Github帳號

  - 直接上github官網申請帳號,記得搭配安裝git或是github desktop

* 安裝nodejs

  - Arch/Manjaro直接用pacman或是yay安裝就好了,Manjaro預設有裝

  ```bash
  pacman -S nodejs
  ```

  - Windows請上官網下載安裝

* 安裝npm

  - Arch/Manjaro

  ```bash
  pacman -S npm
  ```

  - Windows安裝nodejs的時候會一併裝上

* 安裝hexo

  ```bash
  npm install -g hexo-cli
  ```


## Hexo使用

```bash
#在hexo主目錄外建立主目錄<Folder>
hexo init <folder>
```

或是在該`<Folder>` 內

```bash
#在主目錄內直接建立hexo
hexo init
```

## 安裝Hexo Plugin

### 基本命令

```bash
#安裝
npm install <plugin> --save
#移除
npm uninstall <plugin> --save
#更新plugin & Framework,需在Hexo目錄
npm update
```

#### 本站有用的Plugin

* [hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)
* [hexo-renderer-markdown-it-plus](https://github.com/CHENXCHEN/hexo-renderer-markdown-it-plus)

## 設定Hexo

hexo的設定是把本體跟主體的設定分開,各使用一個`_config.yml`檔來定義的

##### Site Section

```c
title:			//網站的主標題	ex:Kiwi's Blog    
subtitle:		//網站的副標題	ex:中年大叔的自言自語
description:	//網站敘述
keywords:		//網站關鍵字
author: 		//網站作者		ex:Kiwi
language: 		//網站語言		ex:zh_TW
timezone:		//網站時區		
```

##### URL Section

```c
url:			//你的網站位置			   ex:https://kiwi0093.github.io/
root: /			//你的網頁在該url下的哪一層	ex:/ as root
```

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
  branch: master
```

## 安裝Next主題

官網上面有兩種安裝方式可以使用`npm`或是`git clone`,因為需要調整Next的設定所以採用git clone方式

```bash
#移動到hexo的主目錄下
cd to <folder>
#Git clone next主題
git clone https://github.com/next-theme/hexo-theme-next themes/next
```

修改Hexo的`_config.yml`將theme定義為next

```yml
theme: next
```

## 設定Next主題

設定檔在`theme/next/_config.yml`中



## 參考資料

[OHLIA's Wiki](https://ohlia.github.io/Wiki-site/wiki/Hexo/build-blog-by-hexo/)

