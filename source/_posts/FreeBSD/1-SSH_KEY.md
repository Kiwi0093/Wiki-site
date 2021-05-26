---
title: Use SSH Key to improve connection security
tags: [Linux, FreeBSD]
---

#### 參考網頁

[MIS腳印 - Windows 使用 SSH 金鑰免密碼登入 Linux](https://www.footmark.info/linux/centos/windows-ssh-nopassword-linux/)

#### 需求工具

* Windows - [Putty](https://www.putty.org/), [Puttygen](https://www.puttygen.com/)

#### 基本概念

基本上想要利用SSH Key來連線就是需要在Client端有private Key,對應Server上的public Key去進行連線,而Putty,WinSCP這些程式吃Putty的ppk格式,所以建立好的private key還需要轉換成ppk格式才能讓這些client使用

#### 作法

有兩個方法都可以達成

1. 從Linux/FreeBSD生成Key,然後用Puttygen轉換成ppk檔
2. 從Puttygen生成Key,然後把public Key轉貼到Linux/FreeBSD上

##### 從Linux/FreeBSD生成Key

```zsh
#確認你在登入的帳號下
ssh-keygen -b 4096 -t rsa
```

ssh-keygen其實就可以了,後面的參數是指定一些條件

-b 4096 這是採取4096 bits的加密編碼

-r rsa  這是指定產生的是rsa格式的key

執行後會產生

```zsh
~/.ssh/id_rsa
~/.ssh/id_rsa.pub
```

這兩個檔案

id_rsa是Private Key, id_rsa是public key

然後把public key變成*sshd_config*內定義的檔案

```zsh
#預設的在/etc/ssh/sshd_config內
AuthorizedKeysFile      .ssh/authorized_keys
```

若不打算修改檔名那就需要修改public key的檔名就好了(複數Key的使用我沒試過但是大概都是放在同一個檔裡面就好了)

```zsh
#使用預設的設定
mv ~/.ssh/id_rsa.pub ~/.ssh/authorized_keys
```

由於ssh的權限設定要求所以需要調整權限

```zsh
chmod 700 ~/.ssh
chmod 644 ~/.ssh/authorized_keys
```

然後利用sftp把id_rsa檔弄到你的windows裡面去,利用puttygen轉換檔案

![Puttygen-import](https://raw.githubusercontent.com/Kiwi0093/graph/master/img/puttygen-1.png)

讀進去後,可以加上自己的密碼

![Puttygen-save](https://raw.githubusercontent.com/Kiwi0093/graph/master/img/puttygen-2.png)

鍵入自己想要的密碼後Save as a ppk file就好了

##### 從Puttygen生成

![puttygen-generate](https://raw.githubusercontent.com/Kiwi0093/graph/master/img/puttygen-3.png)

基本上是一樣的在右下角設定好bits,確認是哪一種,然後按下Generate

就會產生Private key跟Public Key

之後只要儲存ppk檔的Private key,跟把public key的內容塞進~/.ssh/authorized_keys

就算完工,要是一個Client要管很多台,其實用puttygen生成後去塞public Key是比較方便也比較快的

##### sshd_config

要讓SSHD接受KEY並且取消密碼登入(換言之就是只吃Key)的設定方法

```zsh
#我只打算port 22可以接受ssh
Port 22

#禁止Root帳號透過ssh登入
PermitRootLogin no

#接受Public Key認證方式
PubkeyAuthentication yes
#預設的Public Key位置檔名
AuthorizedKeysFile      .ssh/authorized_keys

#不使用Build-in的密碼認證系統也禁止空白密碼
PasswordAuthentication no
PermitEmptyPasswords no

#禁止使用密碼認證登入
ChallengeResponseAuthentication no
UsePAM no
```

改好後重新啟動sshd就好了