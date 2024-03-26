> 原文：https://jvns.ca/blog/2017/01/31/whats-tls/

# TLS：SSL的最新版本

我曾困惑于TLS是什么，基本上SSL的最新版本被称为TLS（SSL 3.0之后的版本就是TLS1.0），为了避免混淆，我还是称其为SSL。

# 什么是证书？

假设我在 https://mail.google.com/ 检查我的邮件，`mail.google.com`是运行在443端口上的HTTPS服务，我想要确定我正在访问`mail.google.com`而不是互联网上其他恶意的服务。

证书的概念曾经困惑了我很久，一天我的同事Ray告诉我可以通过命令行连接服务器并下载它的证书。（如果你想看SSL证书，也可以点击浏览器的绿色锁标志，获取你需要的信息，他们是可信的）

让我们来看一下`mail.google.com`的证书，并尝试解构它。

首先运行`openssl s_client -connect mail.google.com:443`

这会打印很多信息，我们只要关注证书部分：

```
$ openssl s_client -connect mail.google.com:443
...
-----BEGIN CERTIFICATE-----
MIIElDCCA3ygAwIBAgIIMmzfdZnO9pMwDQYJKoZIhvcNAQELBQAwSTELMAkGA1UE
BhMCVVMxEzARBgNVBAoTCkdvb2dsZSBJbmMxJTAjBgNVBAMTHEdvb2dsZSBJbnRl
cm5ldCBBdXRob3JpdHkgRzIwHhcNMTcwMTE4MTg1MjExWhcNMTcwNDEyMTg1MDAw
WjBpMQswCQYDVQQGEwJVUzETMBEGA1UECAwKQ2FsaWZvcm5pYTEWMBQGA1UEBwwN
TW91bnRhaW4gVmlldzETMBEGA1UECgwKR29vZ2xlIEluYzEYMBYGA1UEAwwPbWFp
bC5nb29nbGUuY29tMIIBIjANBgkqhkiG9w0BAQEFAAOCAQ8AMIIBCgKCAQEAiYcr
C9Rn7g9xjsg7khqfRPxUnvpgGyCHqJMXxZGtdf+G02d07cPlMEeaGG12vHyVfRZD
tc/F1ZfwenH6gf0uMobtgw7n2NQa7T7qxuqSUDhZsO1sI1LL/Yqy8QHoooOZQWMz
ytuRA18zti4vQV1dCijADh0+NWI1GDUAKidbaH/fBRrStqBev5Bhq3ZaGj3fDjAO
7CG0Wk3n4Ov2yg44XOdgkLMzjdnbV8l6cZDC7lCK1VsEU1mEd0O0Dw4OcnHLuBPw
IkioZayhPOXDXUS+bhpmtEiCkt8kbHG6jNMC4m8t62Jaf/Si3XNcHhDa4wPCTvid
X//PuuNlRZVg3NjK/wIDAQABo4IBXjCCAVowHQYDVR0lBBYwFAYIKwYBBQUHAwEG
CCsGAQUFBwMCMCwGA1UdEQQlMCOCD21haWwuZ29vZ2xlLmNvbYIQaW5ib3guZ29v
Z2xlLmNvbTBoBggrBgEFBQcBAQRcMFowKwYIKwYBBQUHMAKGH2h0dHA6Ly9wa2ku
Z29vZ2xlLmNvbS9HSUFHMi5jcnQwKwYIKwYBBQUHMAGGH2h0dHA6Ly9jbGllbnRz
MS5nb29nbGUuY29tL29jc3AwHQYDVR0OBBYEFI69aYCEtb2swbJJR3cMOTdcfvZ4
MAwGA1UdEwEB/wQCMAAwHwYDVR0jBBgwFoAUSt0GFhu89mi1dvWBtrtiGrpagS8w
IQYDVR0gBBowGDAMBgorBgEEAdZ5AgUBMAgGBmeBDAECAjAwBgNVHR8EKTAnMCWg
I6Ahhh9odHRwOi8vcGtpLmdvb2dsZS5jb20vR0lBRzIuY3JsMA0GCSqGSIb3DQEB
CwUAA4IBAQAhiqQIwkGp1NmlLq89gjoAfpwaapHuRixxl2S54fyu/4WOHJJafqVA
Tya9J7GTUCyQ6nszCdVizVP26h9TKOs9LJw5jWV9SOnPU2UZKvrNnOUi2FUkCcuD
lsADdKSXNzye3jB88TENrWC/y3ysPdAgPO/sXzyRvNw8SVKl2+RqMDpSRpBptF9e
Lp+WLAM3xKS5SPwCNdCiA332o7qiKRKQm/6bbIWnm7hp/ZnLxbyKaIVytRdiwRNp
O/TTpRv2C708GA3PH6i1pYE86xm3w7lGhN9OiCZpKOJD6ZUH3W20idgPKYPBCO/N
Op2AF3I4iUGeQjXFVLgS6mjUvdLndL9G
-----END CERTIFICATE-----
```
目前为止，这些东西是难以理解的，实际上着这些东西是`X509`格式的，`openssl`命令可以对其解码。

把这些信息保存为`cert.pem`文件，[文件副本](https://gist.githubusercontent.com/jvns/2c249b8059c0b183c02bb3a71e12e418/raw/b4afd1877471b72f8900b2396c30bc670a5701c9/mail_google_cert.pem)你也可以直接下载我保存的这个。

下一个任务是转义这个证书，这样做：
```
$ openssl x509 -in cert.pem -text

Certificate:
    Data:
        Version: 3 (0x2)
        Serial Number: 3633524695565792915 (0x326cdf7599cef693)
    Signature Algorithm: sha256WithRSAEncryption
        Issuer: C=US, O=Google Inc, CN=Google Internet Authority G2
        Validity
            Not Before: Jan 18 18:52:11 2017 GMT
            Not After : Apr 12 18:50:00 2017 GMT
        Subject: C=US, ST=California, L=Mountain View, O=Google Inc, CN=mail.google.com
        Subject Public Key Info:
            Public Key Algorithm: rsaEncryption
                Public-Key: (2048 bit)
                Modulus:
                    00:89:87:2b:0b:d4:67:ee:0f:71:8e:c8:3b:92:1a:
                    9f:44:fc:54:9e:fa:60:1b:20:87:a8:93:17:c5:91:
                    .... blah blah blah ............
                    c2:4e:f8:9d:5f:ff:cf:ba:e3:65:45:95:60:dc:d8:
                    ca:ff
                Exponent: 65537 (0x10001)
        X509v3 extensions:
            X509v3 Subject Alternative Name: 
                DNS:mail.google.com, DNS:inbox.google.com

            X509v3 Subject Key Identifier: 
                8E:BD:69:80:84:B5:BD:AC:C1:B2:49:47:77:0C:39:37:5C:7E:F6:78
    Signature Algorithm: sha256WithRSAEncryption
         21:8a:a4:08:c2:41:a9:d4:d9:a5:2e:af:3d:82:3a:00:7e:9c:
         1a:6a:91:ee:46:2c:71:97:64:b9:e1:fc:ae:ff:85:8e:1c:92:
         ......... blah blah blah more goes here ...........
```

这里有很多东西，有几个部分需要理解：

- `CN=mail.google.com`是`common name`，但是你需要反直觉地忽略这个名字，去看`subject alternative name`的部分。
- `expiry date`：过期日期 Apr 12 18:50:00 2017 GMT
- `X509v3 Subject Alternative Name`: 这个证书为以下域名提供支持，包括了mail.google.com 和inbox.google.com,这些都是邮件域名
- `Public Key Info`：mail.google.com的公钥，通过公钥和mail.google.com进行通讯
- `signature`：签名是最重要的东西，基本上每个人都可以生成mail.google.com的证书，但是没有这个签名，即使我给你证书你也没有理由相信这是一个真的证书，这就是签名的用处。

所以让我们来看看什么是证书签名。

# 证书签名

互联网上的证书通常会把两个部分组合起来：
1. 证书（适用的域名和公钥，还有其他信息）
2. 某个机构的签名，等于在说“visa也认为证书没问题”

在/etc/ssl/certs下存了很多证书，这些证书是被信任的，可以让我的计算机签署其他证书。比如`/etc/ssl/certs/Staat_der_Nederlanden_EV_Root_CA.pem`，这是来自荷兰的证书！假设这个机构签署了mail.google.com的证书，我的计算机会认为“这证书不错，而且荷兰CA也这么认为”。

如果大街上随机一个人签署了证书，我的计算机则会说“我不知道这是哪位仁兄签署的”，并拒绝该证书。

mail.google的证书是：
- `s:/C=US/ST=California/L=Mountain View/O=Google Inc/CN=mail.google.com`
- 表明由`Google Internet Authority G2`签署证书
- 表明被 `GeoTrust Global CA`签署证书
- 表明被`Equifax Secure Certificate Authority`签署证书

我有个/etc/ssl/certs/GeoTrust_Global_CA.pem文件，这解释了为什么我信任mail.google.com证书（Geotrust 信任谷歌证书，谷歌签署了mail.google.com）

# 如何发行一个证书？

发行一个证书的过程通常是这样的：
1. 自己生成证书的第一部分（过期日期、公钥），这部分是公开可见的
2. 同时生成证书的私钥，你要自行保存私钥并不能泄露出去，每次建立SSL连接时需要用到私钥。
3. 向CA机构申请给你的证书签名（通常要付费），CA机构应该要验证证书可信度，也就是要验证申请者具有该域名的所有权。
4. 你将证书配置到你的网站上，这样就可以证明你是你了！

证书机构需要验证域名的完整和真实性，所以当出现Symantec那种CA机构的问题时，人们会很糟心（Symantec为未经验证的域名提供测试证书）

# 透明证书

最后一部分我想谈谈透明证书的问题，这件事很有趣，如你有一个网站我可以肯定你大概率会遇到它。

我们说CA需要具备可信度，但是我的计算机信任很多CA机构，万一其中一个给mail.google.com签署了一个欺诈证书（伪造我是域名拥有者），这可能导致不好的问题。

这不是假设的问题，[certificate transparency](https://www.certificate-transparency.org/what-is-ct)网站里提供了很多个CA被妥协或者出现了错误导致欺诈证书的例子。

解决方法是，Google知道所有mail.google.com的合法证书，可能有一个或多个，透明证书确保如果出现了一个mail.google.com的未知证书，他们会立刻定位出来。

步骤是这样的：
1. 每次CA签署证书时，他们会把它放入公共的`certificate log`，名为证书日志
2. Googlebot 也会把爬取到的网站证书放入`certificate log`
3. 如果一个证书不在log中，浏览器会拒绝该证书（或者即将拒绝）
4. 任何人都可以查看`certificate log`以查找出是不是有欺诈证书

所以如果荷兰的CA签署了一个mail.google.com的欺诈证书，他们必须要把证书放入公共的log中（Google就能发现问题），如果不放入公共log，浏览器会拒绝证书。

# let’s encrypt 进行签署
[let’s encrypt](https://letsencrypt.org/)真的很酷！他们让您拥有您的网站证书并确保它的安全性，甚至不需要了解有关证书在内部工作方式的所有这些信息！而且它是免费的。