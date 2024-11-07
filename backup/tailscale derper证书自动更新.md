derper的acme证书有效期60天，每次过期了要手动更新，很麻烦。看到了https://v2ex.com/t/1071515 帖子自动管理证书，下面试试
**目录：**
- systemd管理derper服务
- certimate 自动管理证书配置
---
### 1. 首先用systemd管理derper服务：
```
[Unit]
Description=Derper Service
After=network.target

[Service]
ExecStart=/root/go/bin/derper --hostname=derper.chajiuqqq.cn -a=:7000 -certmode=manual -http-port=-1 --certdir=/root/.cache/tailscale/derper-certs
Group=root
Restart=always
RestartSec=3
LimitNOFILE=4096

[Install]
WantedBy=multi-user.target
```
Exec命令中
```
/root/go/bin/derper --hostname=xxx.xxx.cn -a=:7000 -certmode=manual -http-port=-1 --certdir=/root/.cache/tailscale/derper-certs
``` 
`--hostname`指定了`xxx.xxx.cn`为域名，监听地址为`:7000 `，证书使用手动指定方式，证书在`/root/.cache/tailscale/derper-certs`下. `-http-port=-1`表示不监听80端口。

### 2. 使用https://github.com/usual2970/certimate 自动管理证书
- 下载二进制文件，部署`nohup ./certimate serve --http "0.0.0.0:8090" &`
- 访问8090端口
- 阿里云域名，设置好ac和ak自动验证域名所有权
![image](https://github.com/user-attachments/assets/23218eeb-270d-4db8-b53f-39debca98523)

- ssh部署，选择密码或者私钥用于连接和部署
![image](https://github.com/user-attachments/assets/6a4ee66c-9533-4f90-90fd-6b31d8f1ed39)
- 注意证书路径需要为`certdir`指向的，`sudo systemctl restart derper`作为更新证书后的命令重启derper
![image](https://github.com/user-attachments/assets/0f5f2dfd-ec4e-4f74-b71f-546d7907126f)


### 3. 效果，显示到期时间以及到期后自动部署：
![image](https://github.com/user-attachments/assets/6e93f103-16d8-4659-b099-6094feba39be)
![image](https://github.com/user-attachments/assets/fd92f81f-26fd-4880-b691-fde7f0b19f2a)

