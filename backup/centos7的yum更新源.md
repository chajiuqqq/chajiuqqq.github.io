官方可能不支持centos7了，太老了，所以用阿里云的源
## 下载源
1、安装wget
yum install -y wget

2、下载CentOS 7的repo文件
`wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo`
或者
`curl -o /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo`

3、更新镜像源
清除缓存：`yum clean all`
生成缓存：`yum update`

> https://developer.aliyun.com/article/704987 http改为https？有啥用