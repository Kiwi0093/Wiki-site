---
title: Markdown語法
tags: [Markdown]
---

# 前言

很久以前當我剛開始寫Blog的時候,~~曾經把我常用的語法整理過一遍~~(找不到了),但是最近看到一些騷操作就想拿出來獨立整理一篇

<!--more-->

# 基本使用

基本使用的部分基本上Typora都有對應的選項或是快速鍵可以使用,所以就大概記一下就好了

## `#`標題文字

基本上就是

# `#` H1 標題

## `##` H2 標題

### `###` H3 標題

#### `####` H4 標題

##### `#####` H5 標題

###### `######` H6 標題

從H1~H6共有六種尺寸,算是使用Markdown語法時最常用的

## 分隔線

```bash
---
***
+++
```

效果

---

## 連結

```Bash
[標題](http網址)
```

效果

[Kiwi's Wiki](https://kiwi0093.github.io/Wiki-site/	"說明文字") 

## 文字效果
|語法1|語法2|效果|
|:-:|:-:|:-:|
|\*斜體\*|\_斜體\_| *斜體*|
|\**粗體\**|\__粗體\__|**粗體**|
|\***粗斜體\***|\___粗斜體\___|***粗斜體***|
|\~~刪除\~~||~~刪除~~|

## 清單

### 無序清單

語法

```
* 第一項
* 第二項
* 第三項
```

效果

* 第一項
* 第二項
* 第三項

\* & \- & \+ 都是一樣的

### 有序清單

語法

```
1. 第一項
2. 第二項
3. 第三項
```

效果

1. 第一項
2. 第二項
3. 第三項

順序顛倒也沒關係會自動排序

## 圖片或是影音連結

語法

```
#一般圖片連結
![Alt text](/path/to/img.jpg)
#youtube連結
[![Dr.Berg的斷食說明](http://img.youtube.com/vi/SlzBMJvtGHo/0.jpg)](https://www.youtube.com/watch?v=SlzBMJvtGHo "斷食體內變化")
```

效果(如youtube範例)

[![Dr.Berg的斷食說明](http://img.youtube.com/vi/SlzBMJvtGHo/0.jpg)](https://www.youtube.com/watch?v=SlzBMJvtGHo "斷食體內變化")

# 進階使用

基本上就是利用HTML的語法直接使用,markdown可以直接使用html語法,在\<>\<>tag中的東西不會被變成Markdown

**HTML 語法盡量包在 Markdown 語法裡面**

## 文字效果

### 底線

| 語法           | 效果         |
| :--------------: | :------------: |
| \<u>底線\</u>      | <u>底線</u> |
|\<del>刪除\</del>|<del>刪除</del>|

### 字體顏色

| 語法                                | 顯示結果 | 語法                                | 顯示結果 |
| :------------------: | :-------: | :------: | :-------: |
| `<font color=#800000>酒紅色</font>` | <font color=#800000>酒紅色</font>   | `<font color=#FF0000>紅色</font>`   | <font color=#FF0000>紅色</font>     |
| `<font color=#FF6600>橘色</font>`   | <font color=#FF6600>橘色</font>     | `<font color=#FFD700>金色</font>`   | <font color=#FFD700>金色</font>     |
| `<font color=#FFFF00>黃色</font>`   | <font color=#FFFF00>黃色</font>     | `<font color=#00FF00>綠色</font>`   | <font color=#00FF00>綠色</font>    |
| `<font color=#008000>墨綠色</font>` | <font color=#008000>墨綠色</font>   | `<font color=#00FFFF>青色</font>`   | <font color=#00FFFF>青色</font>     |
| `<font color=#0000FF>深藍色</font>` | <font color=#0000FF>深藍色</font>   | `<font color=#FF00FF>粉紅色</font>` | <font color=#FF00FF>粉紅色</font>  |
| `<font color=#800080>紫色</font>`   | <font color=#800080>紫色</font>     | `<font color=#808080>灰色</font>`   | <font color=#808080>灰色</font>     |

### 上下標

|語法|效果|
|:-:|:-:|
|3\<sup>2\</sup>=9|3<sup>2</sup>=9|
|H\<sub>2\</sub>O|H<sub>2</sub>O|

### 特殊符號
|語法|效果|
|:-:|:-:|
|商標\&reg;|商標&reg;|
|\&fnof;(X)=X+1|&fnof;(X)=X+1|
|\&radic;2|&radic;2|
|45\&deg;|45&deg;|

## 注音

語法

```html
<ruby>
注<rp>(</rp><rt>ㄓㄨˋ</rt><rp>)</rp>
音<rp>(</rp><rt>ㄧㄣˉ</rt><rp>)</rp>
</ruby>
```

效果

<ruby>
注<rp>(</rp><rt>ㄓㄨˋ</rt><rp>)</rp>
音<rp>(</rp><rt>ㄧㄣˉ</rt><rp>)</rp>
</ruby>

應用方法

```html
我很喜歡<ruby><font color=blue><del>我家的艦隊</del></font><rp>(</rp><rt><font color=red>VMware Esxi</font></rt><rp>)</rp></ruby>
```

#### 我很喜歡<ruby><font color=blue><del>我家的艦隊</del></font><rp>(</rp><rt><font color=red>VMware Esxi</font></rt><rp>)</rp></ruby>

\* 這種用法的時候在\<>\</>內的區塊就全部用Html語法才不會有問題

# 參考文獻

[西灣筆記](https://xiwan.io/archive/markdown-html-common-syntax-summary.html)

[馬力歐的部落格](https://ed521.github.io/2019/08/hexo-markdown/)