递归复制，指定私钥，指定端口
```
scp -r  -i huyw_private.huyw_private -P 8022 /usr/local/mysql/var/tg/stat_get.frm root@10.11.1.91:/var/lib/mysql/tg
```

免密方式：
1. 生成密钥对：
```
ssh-keygen -t rsa -b 4096 -N "" -f ./id_rsa_deploy
cat id_rsa_deploy.pub

```
2. 在目标机器上导入公钥：
```
mkdir -p ~/.ssh
touch ~/.ssh/authorized_keys
# 复制公钥到该文件

# 改权限
chmod 600 ~/.ssh/id_rsa
```