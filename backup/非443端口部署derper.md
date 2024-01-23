非443端口没法使用let's encrypt申请证书，需要自己准备证书，以下使用digiCert的证书并介绍配置步骤。

1. 申请crt证书，放在`/root/.cache/tailscale/derper-certs/`下，命名为`derper.xxx.xx.crt`
2. 运行命令。这里把http禁用了，不然会提示占用80；然后certmode选manual才会去上面的路径去找crt
```
derper -hostname=derper.xxx.xxx -a=:9000 -certmode=manual -http-port=-1
```
最后会提示：
```
2024/01/23 14:35:54 no config path specified; using /var/lib/derper/derper.key
2024/01/23 14:35:54 derper: serving on :9000 with TLS
2024/01/23 14:35:54 running STUN server on [::]:3478

```
说明9000上跑了tls，成功。(注意如果是阿里云等需要在控制台开启端口放行)

3. admin console上配置好derper map:

```
"901": {
				"RegionID":   901,
				"RegionCode": "CN",
				"RegionName": "China",
				"Nodes": [
					{
						"Name":     "aliyun",
						"RegionID": 901,
						"HostName": "derper.xxx.xx",
						"DERPPort": 9000,
					},
				],
			},
```

4. 最后出现relay=CN就成功了

![image](https://github.com/chajiuqqq/chajiuqqq.github.io/assets/50012686/638aaae7-2593-4e09-9a60-786cd6d3ac7a)

## problem shot

客户端运行 `tailscale status`时出现**derper certificate is self-signed**

![image](https://github.com/chajiuqqq/chajiuqqq.github.io/assets/50012686/3ad4b3f7-ec71-4e92-a14f-283cdb29ba5f)

这个问题怀疑是客户端根证书没有被信任，但是访问derper web界面是安全的。后面把网站证书和证书链合并了，就又可以了，可以尝试解决：

```
cat yourSite.crt chain.crt > derper.xxx.xx.crt
```


## 感谢

https://github.com/tailscale/tailscale/issues/7126
