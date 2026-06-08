# 抓包工具

常用的网络抓包与分析工具。

## tcpdump

命令行抓包工具，适用于 Linux 服务器环境。

### 基本抓包

```bash
# 抓取指定网卡的所有包
tcpdump -i eth0

# 抓取指定端口的包
tcpdump -i eth0 port 80

# 抓取指定主机的包
tcpdump -i eth0 host 192.168.1.100

# 抓取指定网段的包
tcpdump -i eth0 net 192.168.1.0/24
```

### 过滤条件

```bash
# 组合过滤：源地址 + 目标端口
tcpdump -i eth0 src 192.168.1.100 and dst port 8080

# 过滤 TCP 协议
tcpdump -i eth0 tcp

# 过滤 UDP 协议
tcpdump -i eth0 udp

# 过滤 ICMP（ping）
tcpdump -i eth0 icmp

# 过滤 HTTP GET 请求
tcpdump -i eth0 -s 0 -A 'tcp[((tcp[12:1] & 0xf0) >> 2):4] = 0x47455420'
```

### 输出控制

```bash
# 显示详细输出
tcpdump -i eth0 -vv

# 不解析主机名和端口名（显示原始 IP 和端口号）
tcpdump -i eth0 -nn

# 以 ASCII 方式显示数据包内容
tcpdump -i eth0 -A

# 以十六进制和 ASCII 方式显示
tcpdump -i eth0 -XX

# 显示数据链路层信息（MAC 地址）
tcpdump -i eth0 -e
```

### 保存与读取

```bash
# 保存抓包结果到文件
tcpdump -i eth0 -w capture.pcap

# 保存指定数量的包
tcpdump -i eth0 -c 1000 -w capture.pcap

# 从文件读取
tcpdump -r capture.pcap

# 保存时限制包大小（snaplen）
tcpdump -i eth0 -s 65535 -w capture.pcap
```

## Wireshark

图形化抓包分析工具，适合深度分析。

### 常用显示过滤器

```
# HTTP 相关
http
http.request.method == "POST"
http.response.code == 200
http.host contains "example.com"

# TCP 相关
tcp.port == 8080
tcp.flags.syn == 1
tcp.flags.rst == 1
tcp.stream eq 0

# DNS 相关
dns
dns.qry.name contains "example.com"

# TLS/SSL
tls.handshake.type == 1
tls.handshake.extensions_server_name contains "example.com"

# IP 相关
ip.addr == 192.168.1.100
ip.src == 192.168.1.100 && ip.dst == 10.0.0.1

# 组合过滤
http && ip.addr == 192.168.1.100
tcp.port == 443 && tls.handshake.type == 1
```

### tshark（Wireshark 命令行版本）

```bash
# 抓取指定接口的包
tshark -i eth0

# 抓取 HTTP 请求
tshark -i eth0 -f "tcp port 80" -Y "http.request"

# 提取 HTTP 请求的 URL
tshark -i eth0 -Y "http.request" -T fields -e http.host -e http.request.uri

# 提取 DNS 查询
tshark -i eth0 -Y "dns.qry.name" -T fields -e dns.qry.name

# 抓包并保存
tshark -i eth0 -w capture.pcap

# 从文件读取并过滤
tshark -r capture.pcap -Y "http"

# 统计 HTTP 状态码
tshark -r capture.pcap -Y "http.response" -T fields -e http.response.code | sort | uniq -c | sort -rn
```

## mitmproxy

交互式 HTTPS 代理，适合 API 调试。

```bash
# 安装
pip install mitmproxy

# 启动代理（默认端口 8080）
mitmproxy

# 启动 Web 界面（默认端口 8081）
mitmweb

# 命令行模式，输出到终端
mitmdump

# 指定监听端口
mitmproxy -p 9090

# 保存抓包流量
mitmdump -w traffic.flow

# 从文件读取并过滤
mitmdump -r traffic.flow --set view_filter="~d example.com"

# 只拦截特定域名
mitmproxy --set view_filter="~d api.example.com"
```

### 常通过滤表达式

| 表达式 | 说明 |
|--------|------|
| `~d example.com` | 匹配域名 |
| `~u /api/users` | 匹配 URL 路径 |
| `~m POST` | 匹配请求方法 |
| `~c 200` | 匹配响应状态码 |
| `~a json` | 匹配 Accept 头 |

## Charles

macOS/Windows 图形化抓包代理工具。

### 基本设置

1. Proxy → Proxy Settings → 设置端口（默认 8888）
2. Help → SSL Proxying → 安装根证书
3. SSL Proxying Settings → 添加 `*:443` 启用 HTTPS 解密

### 常用功能

- **Map Local**：将请求映射到本地文件，用于前端 mock
- **Map Remote**：将请求转发到其他服务器
- **Rewrite**：修改请求/响应头、Body、URL
- **Throttle**：模拟慢速网络
- **Breakpoints**：拦截并修改请求/响应

## 常见场景

### 抓取移动端 HTTP(S) 流量

```bash
# 1. 启动代理
mitmproxy -p 8080
# 或
# Charles / Wireshark

# 2. 手机设置代理
# WiFi 设置 → 手动代理 → 填入电脑 IP 和代理端口

# 3. HTTPS 需要安装 CA 证书
# mitmproxy: 访问 mitm.it 下载证书
# Charles: Help → SSL Proxying → Install on Mobile Device
```

### 抓取 Docker 容器流量

```bash
# 找到容器的网络接口
docker inspect -f '{{range .NetworkSettings.Networks}}{{.NetworkID}}{{end}}' <container>

# 找到网桥接口
ip link show type bridge

# 在宿主机上抓包
tcpdump -i br-xxxxx -nn -w container.pcap
```

### 抓取 SIP 信令（VoIP 场景）

SIP 默认使用 UDP/5060（无加密）或 TCP/5061（TLS），RTP 媒体流使用动态端口。

```bash
# 抓取 SIP 信令（默认端口 5060）
tcpdump -i eth0 -nn -p 5060 -s 0 -w sip.pcap

# 同时抓取 SIP 和 RTP（RTP 端口范围常见为 10000-20000）
tcpdump -i eth0 -nn -s 0 -w sip_rtp.pcap \
  'port 5060 or udp portrange 10000-20000'

# 实时查看 SIP 消息内容
tcpdump -i eth0 -nn -A -s 0 port 5060

# 只看 SIP INVITE 和 200 OK
tcpdump -i eth0 -nn -A -s 0 port 5060 | grep -E '(INVITE|REGISTER|BYE|SIP/2.0)'
```

#### tshark 分析 SIP

```bash
# 提取 SIP 呼叫摘要（方法 + 状态码 + From → To）
tshark -r sip.pcap -Y "sip" -T fields \
  -e frame.time -e ip.src -e ip.dst \
  -e sip.Method -e sip.Status-Line -e sip.From -e sip.To

# 统计 SIP 方法分布
tshark -r sip.pcap -Y "sip.Request" -T fields -e sip.Method | sort | uniq -c | sort -rn

# 查看 SIP 注册请求
tshark -r sip.pcap -Y "sip.Method == REGISTER" -T fields \
  -e ip.src -e sip.From -e sip.Contact -e sip.Expires

# 导出 SIP 对话流（Call-ID 分组）
tshark -r sip.pcap -Y "sip" -T fields -e sip.Call-ID | sort -u
```

#### Wireshark 分析 SIP + RTP

```
# 显示过滤器
sip                                    # 所有 SIP 信令
sip.Method == "INVITE"                 # 仅 INVITE 请求
sip.Status-Line contains "200"         # 仅 200 响应
sip.From contains "1001"               # 来自分机 1001
rtp                                    # 所有 RTP 媒体流

# 分析 RTP 流
# Telephony → RTP → RTP Streams        查看 RTP 流列表
# Telephony → VoIP Calls               查看完整 VoIP 呼叫
# Telephony → SIP Flows                查看 SIP 信令流程图
# Telephony → RTP → Stream Analysis    分析抖动、丢包、延迟
```

### 快速排查 HTTP 接口问题

```bash
# 用 tcpdump 抓包并实时查看 HTTP 头
tcpdump -i any -nn -A -s 0 'tcp port 80 and (((ip[2:2] - ((ip[0]&0xf)<<2)) - ((tcp[12]&0xf0)>>2)) != 0)'

# 用 tshark 提取请求和响应摘要
tshark -i any -f "tcp port 80" -Y "http" -T fields \
  -e frame.time -e ip.src -e ip.dst -e http.request.method \
  -e http.request.uri -e http.response.code
```
