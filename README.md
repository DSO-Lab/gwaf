# 简介

使用 Go 语言开发的 Web 指纹检测引擎。

运行环境:
- 操作系统：Windows X64 / Linux X86_64 / Mac OSX amd64
- 浏览器：Chromium

# 用法

## 参数说明

```
usage: gxwebfinger [<flags>]

Flags:
  -h, --help            Show context-sensitive help (also try --help-long and --help-man).
  -m, --mode="blink"    Work mode: blink or request
  -u, --url=""          Target URL
  -f, --file=""         File path that contains target URLs
  -o, --out=""          Output file of result
  -t, --thread=10       Number of concurrent threads
  -v, --verbose         Include the original request/response in the result
  -r, --rule=""         Fingerprint rule file path
  -c, --custom-rule=""  Custom fingerprint rule file path
  -T, --timeout=10      Timeout per URL request
  -s, --sleep=10        Waiting time between two rules
  -d, --debug=0         Log level: 0-NONE, 1-FATAL, 2-ERROR, 3-WARN, 4-INFO, 5-DEBUG, 6-TRACE
  -F, --fast            Fast mode, raw responses and JS variables not processed
  -R, --render=10       Wait time for page rendering
      --cols=2000       Number of characters allowed per line
      --rows=3000       Total rows allowed to process
  -V, --version         Show application version.    
```

**超时时间说明：** 根据程序设计，一次指纹识别的最大总超时时间大约为 `-T/--timeout` 值的 1.5 ~ 2 倍。

## 命令示例

```
# 单 URL 检测
./gxwebfinger -u https://www.example.com/

# URL 列表文件批量检测
./gxwebfinger -r apps.json -f urls.txt

# 快速检测（忽略原始响应体和JS变量检测）
./gxwebfinger -u https://www.example.com/ -F

# 使用额外的自定义规则检测
./gxwebfinger -c custom.json -u https://www.example.com/
```

## 结果示例

错误：

```json
[
    {
        "url": "https://ssdfjsdfjdsfsdfsf.c",
        "error": "navigation failed: net::ERR_CONNECTION_FAILED",
        "status": 0,
        "time": 0
    }
]
```

有结果：
```json
[
    {
        "url": "https://www.example.com/",
        "title": "https://www.example.com",
        "iconHash": "460fda76b73a833c48ad519778abf7f7",
        "icon": "https://nwzimg.wezhan.cn/favicon.png",
        "certificate": {
            "cn": "*.example.com",
            "issuer": "RapidSSL TLS DV RSA Mixed SHA256 2020 CA-1",
            "san": "*.example.com,example.com",
            "from": "2021-01-15 08:00:00 +0800 CST",
            "to": "2099-01-16 07:59:59 +0800 CST"
        },
        "applications": [
            {
                "categories": [
                    {
                        "id": "22",
                        "name": "Web Servers"
                    }
                ],
                "description": "Apache Httpd。",
                "website": "https://apache.org/",
                "layer": 1,
                "layer_name": "Application Layer",
                "name": "Apache",
                "confidence": "50",
                "custom": 1
            }
        ],
        "headers": {
            "Host": "www.example.com"
        },
        "rawHeaders": "<Base64编码字符串>",
        "html": "<Base64编码字符串>",
        "cookie": {
            "KEY": "VALUE"
        },
        "status": 200
    }
]
```

# 规则库说明

规则库编写方法请参阅 [https://fp.shuziguanxing.com/#/fingerplatinfo](https://fp.shuziguanxing.com/#/fingerplatinfo)

## 默认规则库示例

```
{
      "apps":{
            ".NET": {
                  "cats": [
                        22
                  ],
                  "description": "",
                  "html": "content=\"Visual Basic .NET 7.1",
                  "layer": 1,
                  "website": "https://www.iis.net"
            },
            ... ...
      },
      "categories":{
            "1": {
                  "name": "CMS",
                  "priority": 1
            },
            ... ...
      },
      "layers":{
            "1": {
                  "name": "Application Layer"
            },
            "2": {
                  "name": "Support Layer"
            },
            "3": {
                  "name": "Service Layer"
            },
            "4": {
                  "name": "System Layer"
            },
            "5": {
                  "name": "Hardware Layer"
            }
      }
}
```

## 自定义规则库示例

```
{
    "apps": {
      "Topsec": {
        "cats": [
          1005
        ],
        "cert": "^TOPSEC PRODUCTS$",
        "layer": 1,
        "website": "https://www.citrix.com"
      }
    }
}
```