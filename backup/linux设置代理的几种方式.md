> https://unix.stackexchange.com/a/213740
## [√]使用http_proxy环境变量
修改`~/.bashrc`或者` /etc/profile`:
```bash
http_proxy=http://user:password@proxyserver.com:3128
https_proxy=https://user:password@proxyserver.com:3128
export http_proxy
export https_proxy
```

**http_proxy和https_proxy变量有什么区别？curl命令会如何设置代理？`https_proxy=http://ip:port`这样设置允许吗？**

目标地址使用https时，会使用https_proxy设置代理，但是和代理服务器的通讯方式不一定是https
```bash
export https_proxy=http://192.168.1.1:8080
curl https://example.com
```
这里，curl 通过 HTTP 协议与代理服务器 192.168.1.1:8080 通信，然后代理服务器与 https://example.com 建立 HTTPS 连接。

_nekobox等代理软件不支持https的入站请求，所以代理地址通常都设置为http协议：_
```bash
http_proxy=http://192.168.30.233:2080
https_proxy=http://192.168.30.233:2080
```

## [ ] proxy_chains方式

## [ ] 透明代理（transparent proxy）

