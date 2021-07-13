---
title: Shell Script - echo語法的用法
date: 2020-12-04
tag: [Linux, script]
---

## 參考資料

參考[FLOZz’ MISC](https://misc.flogisoft.com/bash/tip_colors_and_formatting)的說明頁面

<!--more-->

## 基本語法

```
echo -? "you can see this"
```

echo的後面可以加`-e`, `-n` , `-p`等

最常見的是使用`-e`因為加上`-e`後可以讓`""`內的顏色控制碼生效,換行碼生效等等效果

## 內文使用

可以使用

* `\n` - 換行

- `\e` - 跟字型有關的控制碼開頭

#### Formatting

##### Set

| Code | Description                                                  | Example                            | Preview                                                      |
| :--- | :----------------------------------------------------------- | :--------------------------------- | :----------------------------------------------------------- |
| 1    | Bold/Bright                                                  | `echo -e "Normal \e[1mBold"`       | [![Normal Bold](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_01_bright.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_01_bright.png)Normal Bold |
| 2    | Dim                                                          | `echo -e "Normal \e[2mDim"`        | [![Normal Dim](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_02_dim.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_02_dim.png)Normal Dim |
| 4    | Underlined                                                   | `echo -e "Normal \e[4mUnderlined"` | [![Normal Underlined](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_04_underlined.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_04_underlined.png)Normal Underlined |
| 5    | Blink [1)](https://misc.flogisoft.com/bash/tip_colors_and_formatting#fn__1) | `echo -e "Normal \e[5mBlink"`      | [![Normal Blink](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_05_blink.gif)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_05_blink.gif)Normal Blink |
| 7    | Reverse (invert the foreground and background colors)        | `echo -e "Normal \e[7minverted"`   | [![Normal inverted](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_07_inverted.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_07_inverted.png)Normal inverted |
| 8    | Hidden (useful for passwords)                                | `echo -e "Normal \e[8mHidden"`     | [![Normal Hidden](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_08_hidden.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_08_hidden.png)Normal Hidden |

##### Reset

| Code | Description          | Example                                         | Preview                                                      |
| :--- | :------------------- | :---------------------------------------------- | :----------------------------------------------------------- |
| 0    | Reset all attributes | `echo -e "\e[0mNormal Text"`                    | [![Normal Text](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_00_reset_all.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_00_reset_all.png)Normal Text |
| 21   | Reset bold/bright    | `echo -e "Normal \e[1mBold \e[21mNormal"`       | [![Normal Bold Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_21_reset_bright.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_21_reset_bright.png)Normal Bold Normal |
| 22   | Reset dim            | `echo -e "Normal \e[2mDim \e[22mNormal"`        | [![Normal Dim Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_22_reset_dim.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_22_reset_dim.png)Normal Dim Normal |
| 24   | Reset underlined     | `echo -e "Normal \e[4mUnderlined \e[24mNormal"` | [![Normal Underlined Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_24_reset_underlined.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_24_reset_underlined.png)Normal Underlined Normal |
| 25   | Reset blink          | `echo -e "Normal \e[5mBlink \e[25mNormal"`      | [![Normal Blink Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_25_reset_blink.gif)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_25_reset_blink.gif)Normal Blink Normal |
| 27   | Reset reverse        | `echo -e "Normal \e[7minverted \e[27mNormal"`   | [![Normal inverted Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_27_reset_inverted.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_27_reset_inverted.png)Normal inverted Normal |
| 28   | Reset hidden         | `echo -e "Normal \e[8mHidden \e[28mNormal"`     | [![Normal Hidden Normal](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_28_reset_hidden.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_format_28_reset_hidden.png)Normal Hidden Normal |

#### 8/16 colors

##### Foreground (text)

| Code | Color                    | Example                                 | Preview                                                      |
| :--- | :----------------------- | :-------------------------------------- | :----------------------------------------------------------- |
| 39   | Default foreground color | `echo -e "Default \e[39mDefault"`       | [![Default Default](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_39_default.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_39_default.png)Default Default |
| 30   | Black                    | `echo -e "Default \e[30mBlack"`         | [![Default Black](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_30_black.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_30_black.png)Default Black |
| 31   | Red                      | `echo -e "Default \e[31mRed"`           | [![Default Red](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_31_red.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_31_red.png)Default Red |
| 32   | Green                    | `echo -e "Default \e[32mGreen"`         | [![Default Green](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_32_green.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_32_green.png)Default Green |
| 33   | Yellow                   | `echo -e "Default \e[33mYellow"`        | [![Default Yellow](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_33_yellow.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_33_yellow.png)Default Yellow |
| 34   | Blue                     | `echo -e "Default \e[34mBlue"`          | [![Default Blue](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_34_blue.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_34_blue.png)Default Blue |
| 35   | Magenta                  | `echo -e "Default \e[35mMagenta"`       | [![Default Magenta](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_35_magenta.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_35_magenta.png)Default Magenta |
| 36   | Cyan                     | `echo -e "Default \e[36mCyan"`          | [![Default Cyan](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_36_cyan.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_36_cyan.png)Default Cyan |
| 37   | Light gray               | `echo -e "Default \e[37mLight gray"`    | [![Default Light gray](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_37_light_gray.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_37_light_gray.png)Default Light gray |
| 90   | Dark gray                | `echo -e "Default \e[90mDark gray"`     | [![Default Dark gray](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_90_dark_gray.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_90_dark_gray.png)Default Dark gray |
| 91   | Light red                | `echo -e "Default \e[91mLight red"`     | [![Default Light red](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_91_light_red.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_91_light_red.png)Default Light red |
| 92   | Light green              | `echo -e "Default \e[92mLight green"`   | [![Default Light green](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_92_light_green.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_92_light_green.png)Default Light green |
| 93   | Light yellow             | `echo -e "Default \e[93mLight yellow"`  | [![Default Light yellow](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_93_light_yellow.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_93_light_yellow.png)Default Light yellow |
| 94   | Light blue               | `echo -e "Default \e[94mLight blue"`    | [![Default Light blue](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_94_light_blue.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_94_light_blue.png)Default Light blue |
| 95   | Light magenta            | `echo -e "Default \e[95mLight magenta"` | [![Default Light magenta](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_95_light_magenta.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_95_light_magenta.png)Default Light magenta |
| 96   | Light cyan               | `echo -e "Default \e[96mLight cyan"`    | [![Default Light cyan](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_96_light_cyan.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_96_light_cyan.png)Default Light cyan |
| 97   | White                    | `echo -e "Default \e[97mWhite"`         | [![Default White](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_97_white.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_fg_97_white.png)Default White |

##### Background

| Code | Color                    | Example                                  | Preview                                                      |
| :--- | :----------------------- | :--------------------------------------- | :----------------------------------------------------------- |
| 49   | Default background color | `echo -e "Default \e[49mDefault"`        | [![Default Default](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_49_default.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_49_default.png)Default Default |
| 40   | Black                    | `echo -e "Default \e[40mBlack"`          | [![Default Black](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_40_black.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_40_black.png)Default Black |
| 41   | Red                      | `echo -e "Default \e[41mRed"`            | [![Default Red](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_41_red.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_41_red.png)Default Red |
| 42   | Green                    | `echo -e "Default \e[42mGreen"`          | [![Default Green](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_42_green.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_42_green.png)Default Green |
| 43   | Yellow                   | `echo -e "Default \e[43mYellow"`         | [![Default Yellow](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_43_yellow.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_43_yellow.png)Default Yellow |
| 44   | Blue                     | `echo -e "Default \e[44mBlue"`           | [![Default Blue](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_44_blue.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_44_blue.png)Default Blue |
| 45   | Magenta                  | `echo -e "Default \e[45mMagenta"`        | [![Default Magenta](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_45_magenta.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_45_magenta.png)Default Magenta |
| 46   | Cyan                     | `echo -e "Default \e[46mCyan"`           | [![Default Cyan](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_46_cyan.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_46_cyan.png)Default Cyan |
| 47   | Light gray               | `echo -e "Default \e[47mLight gray"`     | [![Default Light gray](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_47_light_gray.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_47_light_gray.png)Default Light gray |
| 100  | Dark gray                | `echo -e "Default \e[100mDark gray"`     | [![Default Dark gray](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_100_dark_gray.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_100_dark_gray.png)Default Dark gray |
| 101  | Light red                | `echo -e "Default \e[101mLight red"`     | [![Default Light red](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_101_light_red.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_101_light_red.png)Default Light red |
| 102  | Light green              | `echo -e "Default \e[102mLight green"`   | [![Default Light green](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_102_light_green.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_102_light_green.png)Default Light green |
| 103  | Light yellow             | `echo -e "Default \e[103mLight yellow"`  | [![Default Light yellow](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_103_light_yellow.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_103_light_yellow.png)Default Light yellow |
| 104  | Light blue               | `echo -e "Default \e[104mLight blue"`    | [![Default Light blue](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_104_light_blue.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_104_light_blue.png)Default Light blue |
| 105  | Light magenta            | `echo -e "Default \e[105mLight magenta"` | [![Default Light magenta](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_105_light_magenta.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_105_light_magenta.png)Default Light magenta |
| 106  | Light cyan               | `echo -e "Default \e[106mLight cyan"`    | [![Default Light cyan](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_106_light_cyan.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_106_light_cyan.png)Default Light cyan |
| 107  | White                    | `echo -e "Default \e[107mWhite"`         | [![Default White](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_107_white.png)](https://misc.flogisoft.com/_media/bash/colors_format/vt100_color_bg_107_white.png)Default White |