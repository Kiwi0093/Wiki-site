---
title: Shell Script - Case迴圈
date: 2020-12-04
tag: [Linux, script]
---

## Case迴圈

```
echo -e "想要給你看的a)A b)B c)C"
while:
do
	read 變數
	case $變數 in
		a)
			選A要做的事情
			break
			;;
		b)
			選B要做的事情
			continue
			;;
		c)
			選C要做的事情
			exit
			;;
		*)
			選ABC以外任意鍵要做的事情
			exit
			;;
	esac
done
```

這是應用了`echo`,`while`,`do`,`case`做出來的讓人選ABC,然後依照其輸入的選擇做反應的寫法

* break - 離開迴圈繼續跑後面的東西
* continue - 回到迴圈的頭重新等待輸入
* exit - 離開這個script