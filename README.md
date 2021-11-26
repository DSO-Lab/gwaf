# GWAF

> 一个使用 Go 语言开发的 Web 应用指纹识别引擎。本引擎基于 Chromium 浏览器来收集 Web 应用信息，然后分别从 `响应头`、`响应页面`、`JS 变量`、`JS 文件`, `收藏图标`、`SSL 证书`、`robots.txt` 等维度来进行 Web 应用的指纹识别。

**运行环境**

- 操作系统：Windows X64 / Linux X86_64 / Mac OSX amd64
- 浏览器：  Chromium（首次运行时程序会自动下载，请耐心等待）

# 目录结构

```
<目录>
 |-- gwaf_<os>_<arch>       # 主程序
 |-- apps.json              # 主指纹库文件
 |-- custom.json            # 自定义规则库文件
```

**提示**: 本引擎的指纹库与 [Wappalyzer <= 6.2.3](https://github.com/AliasIO/wappalyzer/blob/v6.2.3/src/apps.json) 版本完全兼容，可直接下载覆盖本引擎的 `apps.json` 主指纹库文件。

# 用法

## 参数说明

```
usage: gwaf [<flags>]

Flags:
  -h, --help            显示帮助信息
  -m, --mode="blink"    工作模式: blink 或 request，默认为 blink
  -u, --url=""          目标 URL
  -f, --file=""         目标 URL 列表文件路径
  -o, --out=""          结果输出文件路径
  -t, --thread=10       并发线程数量
  -v, --verbose         指示在结果中包含原始响应头和响应体
  -r, --rule=""         指纹库文件路径
  -c, --custom-rule=""  自定义指纹库文件路径
  -T, --timeout=10      每个 URL 请求的 超时时间
  -s, --sleep=10        识别两条指纹之间的等待时间
  -d, --debug=0         日志级别: 0-NONE, 1-FATAL, 2-ERROR, 3-WARN, 4-INFO, 5-DEBUG, 6-TRACE
  -F, --fast            快速模式。该模式下原始响应和 JS 变量将不会处理
  -R, --render=10       页面渲染等待时间
      --cols=2000       处理 HTML 页面每行的字符数
      --rows=3000       处理 HTML 页面的总行数
  -V, --version         显示程序版本    
```

**超时时间说明：** 根据程序设计，一次指纹识别的最大总超时时间大约为 `-T/--timeout` 值的 1.5 ~ 2 倍。

## 命令示例

```
# 单 URL 检测
./gwaf -u https://www.example.com/

# URL 列表文件批量检测
./gwaf -r apps.json -f urls.txt

# 快速检测（忽略原始响应体和JS变量检测）
./gwaf -u https://www.example.com/ -F

# 使用额外的自定义规则检测
./gwaf -c custom.json -u https://www.example.com/
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
        "headers": {
            "Host": "www.example.com"
        },
        "certificate": {
            "cn": "*.example.com",
            "issuer": "RapidSSL TLS DV RSA Mixed SHA256 2020 CA-1",
            "san": "*.example.com,example.com",
            "from": "2021-01-15 08:00:00 +0800 CST",
            "to": "2099-01-16 07:59:59 +0800 CST"
        },
        "iconHash": "460fda76b73a833c48ad519778abf7f7",
        "icon": "https://nwzimg.wezhan.cn/favicon.png",
        "applications": [
            {
                "categories": [
                    {
                        "id": "22",
                        "name": "Web Servers"
                    }
                ],
                "description": "Tengine是由淘宝网发起的Web服务器项目。它在Nginx的基础上，针对大访问量网站的需求，添加了很多高级 功能和特性。",
                "website": "http://tengine.taobao.org/",
                "layer": 3,
                "layer_name": "服务层",
                "name": "Tengine",
                "confidence": "50",
                "version": ""
            }
        ],
        "rawHeaders": "<Base64编码字符串>",
        "html": "<Base64编码字符串>",
        "cookie": {
            "KEY": "VALUE"
        },
        "status": 200,
        "time": 4
    }
]
```

# 规则库说明

规则库编写方法请参阅 [https://fp.shuziguanxing.com/#/fingerplatinfo](https://fp.shuziguanxing.com/#/fingerplatinfo)

## 默认规则库示例

[apps.json](apps.json)

## 自定义规则库示例

[custom.json](custom.json)