# systemd管理Caddy
> 写一个service用于管理caddy
1. `sudo nano /etc/systemd/system/caddy.service`
```
[Unit]
Description=Caddy Web Server
Documentation=https://caddyserver.com/docs/
After=network.target

[Service]
User=root
Group=root
ExecStart=/usr/bin/caddy run --config /etc/caddy/Caddyfile --adapter caddyfile
ExecReload=/usr/bin/caddy reload --config /etc/caddy/Caddyfile --adapter caddyfile
TimeoutStopSec=5s
LimitNOFILE=1048576
LimitNPROC=512
PrivateTmp=true
ProtectSystem=full
AmbientCapabilities=CAP_NET_BIND_SERVICE
CapabilityBoundingSet=CAP_NET_BIND_SERVICE

[Install]
WantedBy=multi-user.target

```
2. 启用并启动 Caddy 服务
接下来，使用以下命令启用并启动 Caddy 服务：
```bash
# 重新加载 systemd 配置
sudo systemctl daemon-reload

# 启动 Caddy 服务
sudo systemctl start caddy

# 设置开机自启动
sudo systemctl enable caddy

# 查看日志
journalctl -u caddy -f
```

> 解释下systemctl enable caddy命令做了什么，怎么实现自启动的

`systemctl enable caddy` 命令的作用是将 Caddy 服务设置为**开机自启动**。具体来说，`systemctl enable` 会在系统引导时自动启动 Caddy 服务，而不是每次手动启动。以下是这个命令的作用原理和工作机制：

### 1. `systemctl enable` 的作用
当你运行 `systemctl enable caddy` 时，Systemd 会在适当的位置创建符号链接，以便系统在启动时自动加载和启动 Caddy 服务。

- 具体地说，`systemctl enable` 会在 `/etc/systemd/system/multi-user.target.wants/` 目录下创建一个指向 `/etc/systemd/system/caddy.service` 的符号链接。
- `multi-user.target` 是一个 Systemd 的目标单元（target unit），它表示系统进入多用户模式时会激活的所有服务。启用服务时的目标通常是 `multi-user.target` 或 `graphical.target`。

### 2. Systemd 的自启动机制
Systemd 是现代 Linux 系统的初始化系统（init system），它通过管理单元文件（unit files）来控制服务、挂载点、设备等。`systemctl enable` 命令让 Systemd 知道在开机时应启动哪些服务：

- 当系统启动时，Systemd 会按照引导的目标（通常是 `multi-user.target` 或 `graphical.target`），加载对应的单元文件并启动指定的服务。
- 通过符号链接的方式，将 Caddy 服务文件添加到启动时会加载的服务列表中，这样就能实现开机时自动启动 Caddy。

### 3. 检查服务是否已设置为自启动

可以使用以下命令检查 `caddy` 是否已启用为自启动服务：

```bash
systemctl is-enabled caddy
```

如果输出是 `enabled`，则表示服务已配置为开机自动启动；如果输出是 `disabled`，则说明服务没有配置为自动启动。

### 4. 禁用自启动

如果需要取消自启动，可以使用以下命令：

```bash
sudo systemctl disable caddy
```

这会删除相应的符号链接，使 Caddy 服务在开机时不再自动启动。

### 小结

- `systemctl enable caddy` 通过创建符号链接将 `caddy.service` 文件添加到开机自启动的服务列表。
- Systemd 在启动时会自动加载和启动这些已启用的服务。

# Caddy实现服务暴露（反向代理）

> 如何用caddy将域名转发到内部的端口？比如blog.xxx.cn转到本机的8090端口

1.  安装 Caddy：确保已经安装并运行了 Caddy。

2.  配置 Caddyfile： 打开或创建一个 Caddyfile，通常位于 /etc/caddy/Caddyfile，但你也可以指定其他路径。在 Caddyfile 中，添加以下配置：
```
blog.xxx.cn {
    reverse_proxy 127.0.0.1:8090
}
```
`blog.xxx.cn`：你的域名。
`reverse_proxy`：设置反向代理，把请求转发到 `127.0.0.1:8090`
3.  重启 Caddy 服务： 如果 Caddy 是作为系统服务运行的，可以使用以下命令来重新加载 Caddy 配置：
```
sudo systemctl reload caddy
```

> 规则可以匹配多个域名吗，怎么写

在 Caddy 中，可以使用逗号分隔的方式为多个域名设置相同的规则。这样，多个域名都可以通过相同的反向代理规则访问到指定的目标。以下是实现的方法：
```
a.com, www.a.com, c.com {
    reverse_proxy b.com
}
```
- 自动获取证书：Caddy 会为每个域名自动获取 HTTPS 证书，所以确保这些域名已指向你的 Caddy 服务器 IP 地址，并且服务器开放了 80 和 443 端口。
- 泛域名支持：Caddy 也支持使用泛域名，例如 *.a.com，可以处理子域名。

> caddy是如何自动申请证书的

Caddy 通过集成的自动化流程，为你的网站申请和管理 SSL/TLS 证书，使你的站点可以自动获得 HTTPS 保护。它的自动申请和更新证书功能主要依赖于 [Let's Encrypt](https://letsencrypt.org) 或 [ZeroSSL](https://zerossl.com) 等 ACME 协议兼容的证书颁发机构 (CA)。以下是 Caddy 如何实现自动申请证书的具体流程：

### 1. 自动启用 HTTPS

当 Caddy 看到配置中的域名（如 `example.com`）时，它会自动为该域名启用 HTTPS。这意味着你不需要手动指定 SSL 配置，Caddy 会自动将 HTTP 流量重定向到 HTTPS，并申请证书。

### 2. 使用 ACME 协议自动申请证书

Caddy 使用 ACME 协议与证书颁发机构（如 Let's Encrypt）通信，完成证书申请和管理。ACME（Automatic Certificate Management Environment）是一种自动化流程，用于申请和管理 HTTPS 证书。以下是具体步骤：

   - **域名解析检查**：Caddy 首先检查配置的域名是否已解析到服务器的 IP 地址，以确保 Caddy 能够在域名所有权验证过程中访问到服务器。
   
   - **申请证书**：如果域名解析正确，Caddy 会使用 ACME 协议向 CA 申请证书。通常使用 HTTP-01 或 DNS-01 验证方式来证明服务器对域名的所有权。
   
      - **HTTP-01 验证**：Caddy 在本地启动一个临时 HTTP 服务，在 `.well-known/acme-challenge/` 路径下提供验证文件。CA 会尝试访问此文件以验证域名的所有权。如果验证成功，则会颁发证书。
      - **DNS-01 验证**（可选）：Caddy 支持使用 DNS 验证，这对需要为内网域名或不支持 HTTP 访问的域名申请证书非常有用。Caddy 可以与一些 DNS 提供商的 API 集成，通过添加特定 DNS 记录来完成验证。

### 3. 自动续期证书

Caddy 会定期检查证书的到期时间，并在证书接近到期时（通常是过期前 30 天）自动重新申请新证书。这种自动续期过程确保你的站点始终保持 HTTPS 连接，无需手动干预。

### 4. Caddy 的存储机制

Caddy 默认会将证书和密钥文件保存在本地存储中，通常位于 `/var/lib/caddy` 目录下。这样可以确保证书在服务重启后仍然可用，并能避免重复申请相同证书。

### 5. 证书管理相关的配置

Caddy 允许通过配置文件对证书管理过程进行定制。例如：

   - **自定义 CA**：如果希望使用特定的 CA，可以通过 `acme_ca` 配置来指定。
   - **禁用自动 HTTPS**：如果不希望自动申请证书，可以在配置中禁用 HTTPS。
   - **自定义 DNS 验证**：如果使用 DNS 验证方式，可以在配置中指定使用的 DNS 提供商和凭据。

### 示例 Caddyfile 配置

Caddyfile 的配置示例如下：

```caddy
example.com {
    reverse_proxy localhost:8080
}

another.com {
    reverse_proxy localhost:8081
    tls {
        dns cloudflare {env.CLOUDFLARE_API_TOKEN}  # 使用 Cloudflare DNS 进行 DNS-01 验证
    }
}
```

### 小结

Caddy 的自动证书管理功能使 HTTPS 配置极为简便，无需手动干预。Caddy 负责整个证书生命周期，包括申请、验证、续期和重新加载证书，从而保证网站的安全性和可访问性。