递归复制，指定私钥，指定端口
```
scp -r  -i huyw_private.huyw_private -P 8022 /usr/local/mysql/var/tg/stat_get.frm root@10.11.1.91:/var/lib/mysql/tg
```