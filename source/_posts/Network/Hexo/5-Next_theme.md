---
title: Next Theme的安裝與設定
tags: [Network,Server,Git,Blog]
---

### 安裝

[Next Theme官網](https://github.com/next-theme/hexo-theme-next)上有兩種安裝方式

+ 透過npm直接安裝(需要Hexo5.0以上版本)

  ```bash
  #在Hexo目錄下
  npm install hexo-theme-next
  ```

  安裝後直接變更Hexo目錄的`_config.yml`內的theme部分就可以用了

  ```C
  theme: next
  ```

+ 透過Git指令直接clone整個theme到Hexo的theme目錄

  ```bash
  #在Hexo目錄下
  git clone https://github.com/next-theme/hexo-theme-next themes/next
  ```

  
  安裝後一樣要變更Hexo目錄的`_config.yml`
  
  ```c
  theme: next
  ```

### 設定

###### #Theme Setting

將Theme的`_config.yml`複製為`_config.[Theme_name].yml`

詳細的說明可以參考[官方的文件說明](https://theme-next.js.org/docs/getting-started/configuration.html)

基本上只需要修改下述幾個主要參數就可以了

##### #Choosing Scheme

```
# ---------------------------------------------------------------
# Scheme Settings
# ---------------------------------------------------------------

# Schemes
#scheme: Muse
scheme: Mist
#scheme: Pisces
#scheme: Gemini

# Dark Mode
darkmode: true
```

有四種scheme可以挑選,官網上有範例可以參考[Muse](https://theme-next.js.org/muse/), [Mist](https://theme-next.js.org/mist/), [Pisces](https://theme-next.js.org/pisces/), [Gemini](https://theme-next.js.org/)可以自行挑選後修改設定啟用

另外現在Next官方支援Darkmode,預設是false,修改成true後可以變成darkmode

##### #Menu

```c
# ---------------------------------------------------------------
# Menu Settings
# ---------------------------------------------------------------

menu:
  home: / || fa fa-home
  #about: /about/ || fa fa-user
  tags: /tags/ || fa fa-tags
  categories: /categories/ || fa fa-th
  archives: /archives/ || fa fa-archive
  #schedule: /schedule/ || fa fa-calendar
  #sitemap: /sitemap.xml || fa fa-sitemap
  #commonweal: /404/ || fa fa-heartbeat
  wiki: https://kiwi0093.github.io/Wiki-site/ || fa fa-sitemap

# Enable / Disable menu icons / item badges.
menu_settings:
  icons: true
  badges: false
```

編輯這個部分可以簡易做出Blog框架內的選單,在官方文件內還有介紹出可以分層的Menu寫法,如下

```c
menu:
  home: / || fa fa-home
  archives: /archives/ || fa fa-archive
  Docs:
    default: /docs/ || fa fa-book
    Getting Started:
      default: /getting-started/ || fa fa-flag
      Installation: /installation.html || fa fa-download
      Configuration: /configuration.html || fa fa-wrench
    Third Party Services:
      default: /third-party-services/ || fa fa-puzzle-piece
      Math Equations: /math-equations.html || fa fa-square-root-alt
      Comment Systems: /comments.html || fa fa-comment-alt
```

其他的細部設定就參考官方文件設定即可

在現在的架構下務必要加上

```c
wiki: https://kiwi0093.github.io/Wiki-site/ || fa fa-sitemap
```

才會建立Wiki的Link