---
title: Script After Chroot - Kiwi Private Gateway installation
date: 2020-11-15
tags: [Linux,Arch,Server]
---

## 前提

+ 自己要用的連結為: https://github.com/Kiwi0093/script/blob/master/
+ 相較於Normal的版本,這個script是我自己本人使用,所以會把V2ray設定檔這邊改成下載加密的tar檔案後進行解密解壓複製到該去的地方
+ 預安裝程式部分加上openssl

##### 使用openssl加解密

###### 加密

```bash
# 將目錄下的file文件打包壓縮，密碼設定為password
tar -czvf - files | openssl des3 -salt -k password -out files.tar.gz
```

###### 解密

```bash
# 將目錄下的file.tar.gz進行解密解壓
openssl des3 -d -k password -salt -in files.tar.gz | tar xzvf -
```

## Script差異內容

```bash
#install Tools
echo -e "${COLOR1}Install Packages${NC}"
echo -e "${COLOR1}Microcode/grub/dnsutils/open-vm-tools/vim/v2ray/screen${NC}"
pacman -Sy intel-ucode grub dnsutils open-vm-tools vim v2ray screen openssl wget
echo -e "${COLOR2}Completed${NC}"

#V2ray config.json get
echo -e "${COLOR1}Fetch V2ray files${NC}"
wget https://github.com/Kiwi0093/script/blob/master/
echo -n "${COLOR1}Please Enter your password:${NC}"
read password
openssl des3 -d -k ${password} -salt -in files.tar.gz | tar xzvf -
echo -e "${COLOR1}Move config files to /etc/v2ray${NC}"
mv -f .//*.json /etc/v2ray/
echo -e "${COLOR1}Clean tmp files${NC}"
rm -f ./*.tar.gz
rm -rf ./
echo -e "${COLOR2}Completed${NC}"


```

