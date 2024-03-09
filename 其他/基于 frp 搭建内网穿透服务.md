## 基于 frp 搭建内网穿透服务

### 1.简介

frp 是一个专注于内网穿透的高性能的反向代理应用，支持 TCP、UDP、HTTP、HTTPS 等多种协议，且支持 P2P 通信。可以将内网服务以安全、便捷的方式通过具有公网 IP 节点的中转暴露到公网



### 2.为什么使用frp

通过在具有公网 IP 的节点上部署 frp 服务端，可以轻松地将内网服务穿透到公网，同时提供诸多专业的功能特性，这包括：

- 客户端服务端通信支持 TCP、QUIC、KCP 以及 Websocket 等多种协议。
- 采用 TCP 连接流式复用，在单个连接间承载更多请求，节省连接建立时间，降低请求延迟。
- 代理组间的负载均衡。
- 端口复用，多个服务通过同一个服务端端口暴露。
- 支持 P2P 通信，流量不经过服务器中转，充分利用带宽资源。
- 多个原生支持的客户端插件（静态文件查看，HTTPS/HTTP 协议转换，HTTP、SOCK5 代理等），便于独立使用 frp 客户端完成某些工作。
- 高度扩展性的服务端插件系统，易于结合自身需求进行功能扩展。
- 服务端和客户端 UI 页面。



### 3.frp下载

文档: https://gofrp.org/zh-cn/docs/

下载地址: https://github.com/fatedier/frp/releases

此文档示例为 0.48.0 版本,对应的下载地址是:

[mac-arm](https://github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_darwin_arm64.tar.gz)

[linux-amd64](https://github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_linux_amd64.tar.gz)

[win-amd64](https://github.com/fatedier/frp/releases/download/v0.48.0/frp_0.48.0_windows_amd64.zip)

每个软件包解压后对应的文件列表如下:

frps  # 服务器程序
frpc  # 客户端程序
frps_full.ini  # 服务器全部配置文件
frpc.ini  # 客户端配置文件
frps.ini  # 服务器配置文件
frpc_full.ini # 客户端全部配置文件
LICENSE



### 4.服务器文件配置

1.编辑服务器配置文件 `vim frps.ini`

```bash
#--------------------------------
[common]
bind_port = 7000 # 服务器绑定端口
token=xxxxxxx  # 服务器设置token, 客户端连接服务器,需要此token

log_file=/opt/frp/frps.log  # 记录日志的位置
log_level=error # 记录日志等级
log_max_day=2 # 日记存放最大天数
#--------------------------------
```

2.执行服务器程序 -c 指定配置文件位置  ,nohup 后台执行

`nohup ./frps -c frps.ini &`



### 5.客户端配置

1.编辑服务器配置文件 `vim frpc.ini`

```bash
#--------------------------------
[common]
server_addr = 60.204.151.127 # 服务器ip
server_port = 7000 # 服务段 软件开启的端口
token=chinaIsNo1 # 校验客户端秘钥,与服务器token一致

[ssh]
type = tcp # 传输协议类型
local_ip = 127.0.0.1 # 监听本地端口
local_port = 8080 # 监听本地端口
remote_port = 2333 # 指定远程服务器打开的端口
#--------------------------------
```

2.执行服务器程序 -c 指定配置文件位置  ,nohup 后台执行

`./frpc -c frpc.ini`



### 6.测试

本地主机开启web服务(apache,tomcat,nginx等), 监听端口 8080

通过浏览器访问 60.204.151.127:2333  即刻访问到 本机 8080端口对应的服务



---



## 示例

基于[frp0.5.20](https://github.com/fatedier/frp/releases/download/v0.52.0/frp_0.52.0_linux_amd64.tar.gz)

#### 1. 基于frp的ftp服务

##### 1.1 服务端配置

```bash
vim frps.toml
#--------------------
bindPort = 7000
#--------------------
```

##### 1.2 Systemd 管理 frps服务

```bash
vim /etc/systemd/system/frps.service

#--------------------
[Unit]
Description = frps-server
After = network.target syslog.target
Wants = network.target

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /opt/frp0520/frps -c /opt/frp0520/frps.toml

[Install]
WantedBy = multi-user.target
#--------------------

# 启动frp服务
systemctl start frps
```

##### 1.3 客户端配置

```bash
vim frpc.toml
#--------------------
serverAddr = "60.204.151.127"
serverPort = 7000

[[proxies]]
name = "test_static_file"
type = "tcp"
remotePort = 2333
[proxies.plugin]
type = "static_file"
# 本地文件目录，对外提供访问
localPath = "/Users/linux/Downloads"
# URL 中的前缀，将被去除，保留的内容即为要访问的文件路径
stripPrefix = "ftp"
httpUser = "xxxxxxx"
httpPassword = "xxxxxxx"
#--------------------

# 服务器防火墙开启 2333 端口
# 访问测试 http://60.204.151.127:2333/ftp
```



#### 2. 基于ftp的web服务

##### 1.1 服务端配置

```bash
vim frps.toml
#--------------------
bindPort = 7000
#--------------------
```

##### 1.2 Systemd 管理 frps服务

```bash
vim /etc/systemd/system/frps.service

#--------------------
[Unit]
Description = frps-server
After = network.target syslog.target
Wants = network.target

[Service]
Type = simple
# 启动frps的命令，需修改为您的frps的安装路径
ExecStart = /opt/frp0520/frps -c /opt/frp0520/frps.toml

[Install]
WantedBy = multi-user.target
#--------------------

# 启动frp服务
systemctl start frps
```

##### 1.3 客户端配置

```bash
vim frpc.toml
#--------------------
serverAddr = "60.204.151.127"
serverPort = 7000

[[proxies]]
name = "ssh"
type = "tcp"
localIP = "127.0.0.1"
localPort = 80
remotePort = 2333

# 访问测试 http://60.204.151.127:2333
#--------------------

# 服务器防火墙开启 2333 端口
# 访问测试 http://60.204.151.127:2333/ftp
```