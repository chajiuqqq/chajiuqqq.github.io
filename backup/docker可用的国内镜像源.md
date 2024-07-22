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