---
title: Shell Script - 變數說明
date: 2020-12-04
tag: [Linux, script]
---

## 參考資料

參考[iT邦幫忙-30天不間斷-資工隨筆大雜燴系列第20篇](https://ithelp.ithome.com.tw/articles/10225500)

<!--more-->

## 基本變數定義

```bash
變數=XYZ
${變數}
```

## 自動變數

| **變數名稱** | **說明**                                                     |
| :----------- | :----------------------------------------------------------- |
| $?           | 表示上一個指令的離開狀況，一般指令正常離開會傳回 0。不正常離開則會傳回 1、2 等數值。 |
| $$           | 這一個 shell 的 process ID number                            |
| $!           | 最後一個在背景執行的程式的 process number                    |
| $-           | 這個參數包含了傳遞給 shell 旗標 (flag)。                     |
| $1           | 代表第一個參數，$2 則為第二個參數，依此類推。而 $0 為這個 shell script 的檔名。 |
| $#           | 執行時，給這個 Shell Script 參數的個數                       |
| $*           | 包含所有輸入的參數，$@ 即代表 $1, $2,….直到所有參數結束。$* 將所有參數無間隔的連在一起，存成一個單一的參數。也就是說 $* 代表了 “$1 $2 $3…”。 |
| $@           | 包含所有輸入的參數，$@ 即代表 $1, $2,….直到所有參數結束。$@ 用將所有參數以空白為間隔，存在 $@ 中。也就是說 $@ 代表了 “$1” “$2” “$3”….。 |
| $BASH_ENV    | absolute path of startup file                                |
| $CDPATH      | directories searched by cd                                   |
| $FCEDIT      | absolute path of history editor                              |
| $LINENO      | current line number in shell script                          |
| $LINES       | terminal height                                              |
| $PPID        | process ID of parent                                         |
| $RANDOM      | random integer                                               |
| $SECONDS     | number of seconds since shell started                        |
| $SHELL       | absolute pathname of preferred shell                         |
| $TMOUT       | seconds to log out after lack of use                         |

## 空變數的處理

| **變數**     | **說明**                                                     |
| :----------- | :----------------------------------------------------------- |
| ${var:-word} | 如果變數 var 尚未設定或是 null，則將使用 word 這個值，但不改變 var 變數的內容。 |
| ${var:=word} | 如果變數 var 尚未設定或是 null，則變數 var 的內容將等於 word 這個字串，並使用這個新的值。 |
| ${var:?word} | 如果變數 var 已經設定了，而且不是 null，則將使用變數 var。否則則印出 word 這個字串，並強制離開程式。我們可以設定一個字串 “Parameter null or not set” 來在變數未設定時印出，並終止程式。 |
| ${var:+word} | 如果變數 var 已經設定了，而且不是 null，則以 word 這個字串取代它，否則就不取代。 |

## 輸入型變數

使用`read`指令讀取key入的內容為變數內容如

```bash
echo -n "please input a var for use"
read var
echo -e "${var}"
```

螢幕上會先出現

```bash
please input a var for use
```

然後停住等待輸入,若輸入`good`則會出現

```bash
good
```