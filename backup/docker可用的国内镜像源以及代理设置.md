> https://neucrack.com/p/286
# 源
这几个可用，参考https://gist.github.com/y0ngb1n/7e8f16af3242c7815e7ca2f0833d3ea6
```
sudo mkdir -p /etc/docker
sudo tee /etc/docker/daemon.json <<-'EOF'
{
    "registry-mirrors": [
        "https://docker.m.daocloud.io",
        "https://dockerproxy.com",
        "https://mirror.baidubce.com",
        "https://docker.nju.edu.cn",
        "https://mirror.iscas.ac.cn"
    ]
}
EOF
sudo systemctl daemon-reload
sudo systemctl restart docker
```
# 代理设置
docker pull 拉取镜像时设置代理
docker 的代理设置是一件比较麻烦的事情，很容易出问题，但是弄懂了也还是比较清楚。

首先， docker pull 和 docker build/docker run 使用代理的方式不一样！

docker pull 的代理被 systemd 接管，所以需要设置 systemd…
```
sudo vim /etc/systemd/system/docker.service.d/http-proxy.conf
```
```
[Service]
Environment="HTTP_PROXY=http://127.0.0.1:8123"
Environment="HTTPS_PROXY=http://127.0.0.1:8123"
```
这里的127.0.0.1是直接用了本机的 http 代理，然后重启服务才能生效。

```
sudo systemctl daemon-reload
sudo systemctl restart docker
```
> 如果是wsl2，也写为127.0.0.1，同时.wslconfig里这么配置：
```
[wsl2]
networkingMode=mirrored
dnsTunneling=true
firewall=true
autoProxy=true

[experimental]
hostAddressLoopback=true
```
可以通过`sudo systemctl show --property=Environment docker`看到设置的环境变量。

然后docker pull就会使用代理啦！
