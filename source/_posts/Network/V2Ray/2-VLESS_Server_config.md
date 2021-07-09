---
title: VLESS Server Config
date: 2020-11-27
tags: [Network, V2Ray, VPN]
---

## 前言

VLESS的Server設定裡面已經包括了WS+TLS,所以設定後可以不需要另外設定Web Server(Nginx/Apache)

<!--more-->

## 參考資料

這篇的內容基本上就是直接拿V2Ray團隊的範例直接用,後續有更新再說

[V2fly官方文件](https://www.v2fly.org/config/protocols/vless.html)

[V2fly範例Github](https://github.com/v2fly/v2ray-examples)

## Config.json

```json
{
    "log": {
        "loglevel": "warning"
    },
    "inbounds": [
        {
            "port": 443,
            "protocol": "vless",
            "settings": {
                "clients": [
                    {
                        "id": "", // 填写你的 UUID
                        "level": 0,
                        "email": "love@v2fly.org" //你可以改自己的Email-Address 同上面
                    }
                ],
                "decryption": "none",
                "fallbacks": [
                    {
                        "dest": 80
                    },
                    {
                        "path": "/websocket", // 必须换成自定义的 PATH
                        "dest": 1234, //內部轉的PORT要與上面的一致
                        "xver": 1
                    }
                ]
            },
            "streamSettings": {
                "network": "tcp",
                "security": "tls",
                "tlsSettings": {
                    "alpn": [
                        "http/1.1"
                    ],
                    "certificates": [
                        {
                            "certificateFile": "/path/to/fullchain.crt", // 换成你的证书，绝对路径
                            "keyFile": "/path/to/private.key" // 换成你的私钥，绝对路径
                        }
                    ]
                }
            }
        },
        {
            "port": 1234, //內部轉的PORT要與上面的一致
            "listen": "127.0.0.1",
            "protocol": "vless",
            "settings": {
                "clients": [
                    {
                        "id": "", // 填写你的 UUID
                        "level": 0,
                        "email": "love@v2fly.org" //你可以改自己的Email-Address 同上面
                    }
                ],
                "decryption": "none"
            },
            "streamSettings": {
                "network": "ws",
                "security": "none",
                "wsSettings": {
                    "acceptProxyProtocol": true, // 提醒：若你用 Nginx/Caddy 等反代 WS，需要删掉这行
                    "path": "/websocket" // 必须换成自定义的 PATH，需要和上面的一致
                }
            }
        }
    ],
    "outbounds": [
        {
            "protocol": "freedom"
        }
    ]
}
```

