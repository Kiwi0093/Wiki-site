---
title: Script語法說明
date: 2020-11-15
tags: [Linux,Arch,Server]
---

## 字元定義

參考[iT邦幫忙-30天不間斷-資工隨筆大雜燴系列第20篇](https://ithelp.ithome.com.tw/articles/10225500)

#### 特殊字元

|     符號     |       名稱       |             意義             |
| :----------: | :--------------: | :--------------------------: |
|      $       |       變數       |      取變數名稱對應的值      |
|      #       |       註解       |                              |
|              |     跳脫符號     | 特殊及萬用字元還原成一般字元 |
|      \|      |       管線       |                              |
|      ;       | 分隔連續指令符號 |                              |
|      $       |     工作控制     |      將指令變成背景執行      |
|      /       |     目錄符號     |           分隔路徑           |
|     >,>>     | 資料重導向(輸出) |                              |
|    <，<<     | 資料重導向(輸入) |                              |
|      ''      |      單引號      |         無法取變數值         |
|      ""      |      雙引號      |         可以取變數值         |
| `\` 或是 $() | 裏面包執行的指令 |                              |
|      ()      |                  |        子 shell的始末        |
|      {}      |  中間爲命令區塊  |                              |

#### 萬用字元

| 符號 |                           功能                            |
| :--: | :-------------------------------------------------------: |
|  *   |                    0到無窮多個任意字元                    |
|  ?   |                    一個以上的任意字元                     |
|  []  |                   一個以上括號內的字元                    |
|  -   |   編碼範圍內的所有字元，例如[0-9]代表0到9之間的所有數字   |
|  ^   | 後面接的字元都不要，例如[^0-9]代表0-9之間的所有字元都不接 |

## echo語法

參考[FLOZz' MISC](https://misc.flogisoft.com/bash/tip_colors_and_formatting)的說明頁面

標頭可以使用

- `\e`
- `\033`
- `\x1B`

#### Formatting

##### Set

| Code | Description                                                  | Example                            | Preview                                                      |
| ---- | :----------------------------------------------------------- | :--------------------------------- | :----------------------------------------------------------- |
| 1    | Bold/Bright                                                  | `echo -e "Normal \e[1mBold"`       | ![Normal Bold](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_01_bright.png) |
| 2    | Dim                                                          | `echo -e "Normal \e[2mDim"`        | ![Normal Dim](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_02_dim.png) |
| 4    | Underlined                                                   | `echo -e "Normal \e[4mUnderlined"` | ![Normal Underlined](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_04_underlined.png) |
| 5    | Blink [1)](https://misc.flogisoft.com/bash/tip_colors_and_formatting#fn__1) | `echo -e "Normal \e[5mBlink"`      | ![Normal Blink](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_05_blink.gif) |
| 7    | Reverse (invert the foreground and background colors)        | `echo -e "Normal \e[7minverted"`   | ![Normal inverted](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_07_inverted.png) |
| 8    | Hidden (useful for passwords)                                | `echo -e "Normal \e[8mHidden"`     | ![Normal Hidden](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_08_hidden.png) |

##### Reset

| Code | Description          | Example                                         | Preview                                                      |
| ---- | :------------------- | :---------------------------------------------- | :----------------------------------------------------------- |
| 0    | Reset all attributes | `echo -e "\e[0mNormal Text"`                    | ![Normal Text](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_00_reset_all.png) |
| 21   | Reset bold/bright    | `echo -e "Normal \e[1mBold \e[21mNormal"`       | ![Normal Bold Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_21_reset_bright.png) |
| 22   | Reset dim            | `echo -e "Normal \e[2mDim \e[22mNormal"`        | ![Normal Dim Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_22_reset_dim.png) |
| 24   | Reset underlined     | `echo -e "Normal \e[4mUnderlined \e[24mNormal"` | ![Normal Underlined Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_24_reset_underlined.png) |
| 25   | Reset blink          | `echo -e "Normal \e[5mBlink \e[25mNormal"`      | ![Normal Blink Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_25_reset_blink.gif) |
| 27   | Reset reverse        | `echo -e "Normal \e[7minverted \e[27mNormal"`   | ![Normal inverted Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_27_reset_inverted.png) |
| 28   | Reset hidden         | `echo -e "Normal \e[8mHidden \e[28mNormal"`     | ![Normal Hidden Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_28_reset_hidden.png) |

#### 8/16 colors

##### Foreground (text)

| Code | Color                    | Example                                 | Preview                                                      |
| ---- | :----------------------- | :-------------------------------------- | ------------------------------------------------------------ |
| 39   | Default foreground color | `echo -e "Default \e[39mDefault"`       | ![Default Default](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_39_default.png) |
| 30   | Black                    | `echo -e "Default \e[30mBlack"`         | ![Default Black](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_30_black.png) |
| 31   | Red                      | `echo -e "Default \e[31mRed"`           | ![Default Red](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_31_red.png) |
| 32   | Green                    | `echo -e "Default \e[32mGreen"`         | ![Default Green](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_32_green.png) |
| 33   | Yellow                   | `echo -e "Default \e[33mYellow"`        | ![Default Yellow](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_33_yellow.png) |
| 34   | Blue                     | `echo -e "Default \e[34mBlue"`          | ![Default Blue](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_34_blue.png) |
| 35   | Magenta                  | `echo -e "Default \e[35mMagenta"`       | ![Default Magenta](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_35_magenta.png) |
| 36   | Cyan                     | `echo -e "Default \e[36mCyan"`          | ![Default Cyan](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_36_cyan.png) |
| 37   | Light gray               | `echo -e "Default \e[37mLight gray"`    | ![Default Light gray](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_37_light_gray.png) |
| 90   | Dark gray                | `echo -e "Default \e[90mDark gray"`     | ![Default Dark gray](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_90_dark_gray.png) |
| 91   | Light red                | `echo -e "Default \e[91mLight red"`     | ![Default Light red](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_91_light_red.png) |
| 92   | Light green              | `echo -e "Default \e[92mLight green"`   | ![Default Light green](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_92_light_green.png) |
| 93   | Light yellow             | `echo -e "Default \e[93mLight yellow"`  | ![Default Light yellow](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_93_light_yellow.png) |
| 94   | Light blue               | `echo -e "Default \e[94mLight blue"`    | ![Default Light blue](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_94_light_blue.png) |
| 95   | Light magenta            | `echo -e "Default \e[95mLight magenta"` | ![Default Light magenta](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_95_light_magenta.png) |
| 96   | Light cyan               | `echo -e "Default \e[96mLight cyan"`    | ![Default Light cyan](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_96_light_cyan.png) |
| 97   | White                    | `echo -e "Default \e[97mWhite"`         | ![Default White](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_97_white.png) |

##### Background

| Code | Color                    | Example                                  | Preview                                                      |
| :--- | :----------------------- | :--------------------------------------- | ------------------------------------------------------------ |
| 49   | Default background color | `echo -e "Default \e[49mDefault"`        | ![Default Default](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_49_default.png) |
| 40   | Black                    | `echo -e "Default \e[40mBlack"`          | ![Default Black](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_40_black.png) |
| 41   | Red                      | `echo -e "Default \e[41mRed"`            | ![Default Red](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_41_red.png) |
| 42   | Green                    | `echo -e "Default \e[42mGreen"`          | ![Default Green](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_42_green.png) |
| 43   | Yellow                   | `echo -e "Default \e[43mYellow"`         | ![Default Yellow](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_43_yellow.png) |
| 44   | Blue                     | `echo -e "Default \e[44mBlue"`           | ![Default Blue](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_44_blue.png) |
| 45   | Magenta                  | `echo -e "Default \e[45mMagenta"`        | ![Default Magenta](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_45_magenta.png) |
| 46   | Cyan                     | `echo -e "Default \e[46mCyan"`           | ![Default Cyan](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_46_cyan.png) |
| 47   | Light gray               | `echo -e "Default \e[47mLight gray"`     | ![Default Light gray](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_47_light_gray.png) |
| 100  | Dark gray                | `echo -e "Default \e[100mDark gray"`     | ![Default Dark gray](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_100_dark_gray.png) |
| 101  | Light red                | `echo -e "Default \e[101mLight red"`     | ![Default Light red](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_101_light_red.png) |
| 102  | Light green              | `echo -e "Default \e[102mLight green"`   | ![Default Light green](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_102_light_green.png) |
| 103  | Light yellow             | `echo -e "Default \e[103mLight yellow"`  | ![Default Light yellow](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_103_light_yellow.png) |
| 104  | Light blue               | `echo -e "Default \e[104mLight blue"`    | ![Default Light blue](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_104_light_blue.png) |
| 105  | Light magenta            | `echo -e "Default \e[105mLight magenta"` | ![Default Light magenta](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_105_light_magenta.png) |
| 106  | Light cyan               | `echo -e "Default \e[106mLight cyan"`    | ![Default Light cyan](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_106_light_cyan.png) |
| 107  | White                    | `echo -e "Default \e[107mWhite"`         | ![Default White](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_107_white.png) |

## 變數

#### 基本變數定義

```c
變數=XYZ
${變數}
```

參考[iT邦幫忙-30天不間斷-資工隨筆大雜燴系列第20篇](https://ithelp.ithome.com.tw/articles/10225500)

#### 自動變數

| **變數名稱** | **說明**                                                     |
| ------------ | ------------------------------------------------------------ |
| $?           | 表示上一個指令的離開狀況，一般指令正常離開會傳回 0。不正常離開則會傳回 1、2 等數值。 |
| $$           | 這一個 shell 的 process ID number                            |
| $!           | 最後一個在背景執行的程式的 process number                    |
| $-           | 這個參數包含了傳遞給 shell 旗標 (flag)。                     |
| $1           | 代表第一個參數，$2 則為第二個參數，依此類推。而 $0 為這個 shell script 的檔名。 |
| $#           | 執行時，給這個 Shell Script 參數的個數                       |
| $*           | 包含所有輸入的參數，$@ 即代表 $1, $2,....直到所有參數結束。$* 將所有參數無間隔的連在一起，存成一個單一的參數。也就是說 $* 代表了 "$1 $2 $3..."。 |
| $@           | 包含所有輸入的參數，$@ 即代表 $1, $2,....直到所有參數結束。$@ 用將所有參數以空白為間隔，存在 $@ 中。也就是說 $@ 代表了 "$1" "$2" "$3"....。 |
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

#### 空變數的處理

| **變數**     | **說明**                                                     |
| ------------ | ------------------------------------------------------------ |
| ${var:-word} | 如果變數 var 尚未設定或是 null，則將使用 word 這個值，但不改變 var 變數的內容。 |
| ${var:=word} | 如果變數 var 尚未設定或是 null，則變數 var 的內容將等於 word 這個字串，並使用這個新的值。 |
| ${var:?word} | 如果變數 var 已經設定了，而且不是 null，則將使用變數 var。否則則印出 word 這個字串，並強制離開程式。我們可以設定一個字串 "Parameter null or not set" 來在變數未設定時印出，並終止程式。 |
| ${var:+word} | 如果變數 var 已經設定了，而且不是 null，則以 word 這個字串取代它，否則就不取代。 |

#### 輸入型變數

使用`read`指令讀取key入的內容為變數內容如

```c
echo -n "please input a var for use"
read var
echo -e "${var}"
```

螢幕上會先出現

```c
please input a var for use
```

然後停住等待輸入,若輸入`good`則會出現

```c
good
```

