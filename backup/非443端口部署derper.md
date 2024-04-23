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

---

[4/23 update]

## 使用acme.sh申请证书

derper提供的命令行中，如果443端口可以使用let's encrypt申请证书，但不是443上跑就要手动提供证书。这里补充用acme.sh申请证书并自动更新证书。

1. China方式安装[acme.sh](https://github.com/acmesh-official/acme.sh/wiki/Install-in-China)

```
git clone https://gitee.com/neilpang/acme.sh.git
cd acme.sh
./acme.sh --install -m my@example.com
```

2. [生成证书的几种方式](https://github.com/acmesh-official/acme.sh/wiki/%E8%AF%B4%E6%98%8E#2-%E7%94%9F%E6%88%90%E8%AF%81%E4%B9%A6)

这里用 acme.sh 假装自己是一个webserver, 临时听在80 端口, 完成验证实现证书申请。注意这里-d可以有多个域名，比如`-d example.com`, `-d example.com`，这样生成的证书可以给多个域名使用：
```
acme.sh --issue -d mydomain.com --standalone
```
3. ~/.acme.sh下的域名文件夹中fullchain.cer就是完整的证书链，.key就是私钥。查看fullchain.cer可以看到三段证书，分别是

- 网站域名证书
- Let's Encrypt颁发的中间机构证书
- Let's Encrypt根证书

形成了完整的证书链。

```
cat   fullchain.cer  #I list part of each certificate

-----BEGIN CERTIFICATE-----
MIIFGzCCBAOgAwIBAgISA8r1Ru/nVltRJjbcCA7J7mFGMA0GCSqGSIb3DQEBCwUA
MDIxCzAJBgNVBAYTAlVTMRYwFAYDVQQKEw1MZXQncyBFbmNyeXB0MQswCQYDVQQD
EwJSMzAeFw0yMjA0MTAwNTE2MzNaFw0yMjA3MDkwNTE2MzJaMBUxEzARBgNVBAMT
CmxhaGF3YS54eXowggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEKAoIBAQDwij1T
n6GkUu6Vom80ouqn
-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
MIIFFjCCAv6gAwIBAgIRAJErCErPDBinU/bWLiWnX1owDQYJKoZIhvcNAQELBQAw
TzELMAkGA1UEBhMCVVMxKTAnBgNVBAoTIEludGVybmV0IFNlY3VyaXR5IFJlc2Vh
cmNoIEdyb3VwMRUwEwYDVQQDEwxJU1JHIFJvb3QgWDEwHhcNMjAwOTA0MDAwMDAw
WhcNMjUwOTE1MTYwMDAwWjAyMQswCQYDVQQGEwJVUzEWMBQGA1UEChMNTGV0J3Mg
RW5jcnlwdDELMAkGA1UEAxMCUjMwggEiMA0GCSqGSIb3DQEBAQUAA4IBDwAwggEK
AoIBAQC7AhUozPaglNMPEuyNVZLD+ILxmaZ6QoinXSaqtSu5xUyxr45r+XXIo9cP

-----END CERTIFICATE-----

-----BEGIN CERTIFICATE-----
MIIFYDCCBEigAwIBAgIQQAF3ITfU6UK47naqPGQKtzANBgkqhkiG9w0BAQsFADA/
MSQwIgYDVQQKExtEaWdpdGFsIFNpZ25hdHVyZSBUcnVzdCBDby4xFzAVBgNVBAMT
DkRTVCBSb290IENBIFgzMB4XDTIxMDEyMDE5MTQwM1oXDTI0MDkzMDE4MTQwM1ow
TzELMAkGA1UEBhMCVVMxKTAnBgNVBAoTIEludGVybmV0IFNlY3VyaXR5IFJlc2Vh
cmNoIEdyb3VwMRUwEwYDVQQDEwxJU1JHIFJvb3QgWDEwggIiMA0GCSqGSIb3DQEB
AQUAA4ICDwAwggIKAoICAQCt6CRz9BQ385ueK1coHIe+3LffOJCMbjzmV6B493XC
ov71am72AE8o295ohmxEk7
-----END CERTIFICATE-----
```

> The end-user certificate for the domain you requested (e.g. example.com).
> The intermediate certificate(s) issued by the Let's Encrypt certificate authority.
> The root certificate of the Let's Encrypt certificate authority.


5. 复制到`/root/.cache/tailscale/derper-certs/`下，并重命名为`derper.xxx.xx.crt`和`derper.xxx.xx.key`

[crt文件和cer文件内容是一致的](https://stackoverflow.com/a/642346)，只是扩展名不同，而derper会去寻找crt文件，所以可以直接重命名即可。如果
证书以 `-----BEGIN CERTIFICATE-----`开头，就是把X.509格式证书以base64编码了。否则就是二进制的X.509格式。

7. 运行最上面第二步启动derper即可

**证书更新：**

使用`acme.sh --info -d example.com` 查看证书信息，可以看到`Le_NextRenewTimeStr`为证书下次更新时间。对于derper，需要手动把新的证书复制到上述`/root/.cache/tailscale/derper-certs/`下才能生效（*待确认*）

```
acme.sh --info -d example.com
# 会输出如下内容：
DOMAIN_CONF=/root/.acme.sh/example.com/example.com.conf
Le_Domain=example.com
Le_Alt=no
Le_Webroot=dns_ali
Le_PreHook=
Le_PostHook=
Le_RenewHook=
Le_API=https://acme-v02.api.letsencrypt.org/directory
Le_Keylength=
Le_OrderFinalize=https://acme-v02.api.letsencrypt.org/acme/finalize/23xxxx150/781xxxx4310
Le_LinkOrder=https://acme-v02.api.letsencrypt.org/acme/order/233xxx150/781xxxx4310
Le_LinkCert=https://acme-v02.api.letsencrypt.org/acme/cert/04cbd28xxxxxx349ecaea8d07
Le_CertCreateTime=1649358725
Le_CertCreateTimeStr=Thu Apr  7 19:12:05 UTC 2022
Le_NextRenewTimeStr=Mon Jun  6 19:12:05 UTC 2022
Le_NextRenewTime=1654456325
Le_RealCertPath=
Le_RealCACertPath=
Le_RealKeyPath=/etc/acme/example.com/privkey.pem
Le_ReloadCmd=service nginx force-reload
Le_RealFullChainPath=/etc/acme/example.com/chain.pem
```