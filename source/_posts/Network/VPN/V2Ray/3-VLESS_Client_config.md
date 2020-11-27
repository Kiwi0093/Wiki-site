---
title: VLESS Client Config
date: 2020-11-27
tags: [Network, V2Ray, VPN]
---

## 前言

VLESS的Client設定其實很簡單只需要把原來VMESS的稍微改一下就好了, 因為Client的設定通常會包含Local DNS還有其他的相關設定,所以就不放完整的版本

## 參考資料

這篇的內容基本上就是直接拿V2Ray團隊的範例直接用,後續有更新再說

[V2fly官方文件](https://www.v2fly.org/config/protocols/vless.html)

[V2fly範例Github](https://github.com/v2fly/v2ray-examples)

## Config.json - 差異部份

```json
 "outbounds": [
        {
            "protocol": "vless",
            "settings": {
                "vnext": [
                    {
                        "address": "example.com", // 换成你的域名或服务器 IP（发起请求时无需解析域名了）
                        "port": 443,
                        "users": [
                            {
                                "id": "", // 填写你的 UUID
                                "encryption": "none",
                                "level": 0
                            }
                        ]
                    }
                ]
            },
            "streamSettings": {
                "network": "ws",
                "security": "tls",
                "tlsSettings": {
                    "serverName": "example.com" // 换成你的域名
                },
                "wsSettings": {
                    "path": "/websocket" // 必须换成自定义的 PATH，需要和服务端的一致
                }
            }
        }
    ]
}
```

