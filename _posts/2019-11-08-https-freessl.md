---
layout: post
category: https
title: 五分钟搞定 HTTPS 配置，手把手教
tagline: by 沉默王二
tag: https
---

FreeSSL.cn 是一个免费提供 HTTPS 证书申请、HTTPS 证书管理和 HTTPS 证书到期提醒服务的网站，旨在推进 HTTPS 证书的普及与应用，简化证书申请的流程。

<!--more-->




### 01、关于 FreeSSL.cn

FreeSSL.cn 是一个免费提供 HTTPS 证书申请、HTTPS 证书管理和 HTTPS 证书到期提醒服务的网站，旨在推进 HTTPS 证书的普及与应用，简化证书申请的流程。

当然了，我看重的不是免费，而是 FreeSSL 使用起来非常人性化。我是一个计算机常识非常薄弱的程序员（羞愧一下），但通过 FreeSSL，我竟然可以独自完成 Tomcat 的 HTTPS 配置！

很多年以前，公司要做华夏银行的接口对接，需要 HTTPS 访问，大概花了 3000 块买的证书，最后证书还有问题，HTTPS 也没搞定。总之，坑的很！

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-1.png)

FreeSSL.cn 有很大的不同，申请非常便捷，优点很多，值得推荐一波。毕竟再也不用邮件、电话各种联系了（也许时代进步了）。

- 100% 永久免费；这要感谢 Let's Encrypt 与 TrustAsia 提供的免费 SSL 证书。

- 在 HTTPS 证书到期前，FreeSSL.cn 会及时地提醒更换证书，免费的服务。

- 私钥不在网络中传播，确保 HTTPS 证书的安全。

### 02、使用  FreeSSL 申请证书

**第一步**，填写域名，点击「创建免费的 SSL 证书」

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-2.png)

**第二步**，填写邮箱，点击「创建」

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-3.png)

1）**证书类型**默认为 RSA

RSA 和 ECC 有什么区别呢？可以通过下面几段文字了解一下。

HTTPS 通过 TLS 层和证书机制提供了内容加密、身份认证和数据完整性三大功能，可以有效防止数据被监听或篡改，还能抵御 MITM（中间人）攻击。TLS 在实施加密过程中，需要用到非对称密钥交换和对称内容加密两大算法。

对称内容加密强度非常高，加解密速度也很快，只是无法安全地生成和保管密钥。在 TLS 协议中，应用数据都是经过对称加密后传输的，传输中所使用的对称密钥，则是在握手阶段通过非对称密钥交换而来。常见的 AES-GCM、ChaCha20-Poly1305，都是对称加密算法。

非对称密钥交换能在不安全的数据通道中，产生只有通信双方才知道的对称加密密钥。目前最常用的密钥交换算法有 RSA 和 ECDHE：**RSA** 历史悠久，支持度好，但不支持 PFS（Perfect Forward Secrecy）；而 ECDHE 是使用了 **ECC**（椭圆曲线）的 DH（Diffie-Hellman）算法，计算速度快，支持 PFS。

2）**验证类型**默认为 DNS

**DNS** 和**文件验证**有什么区别呢？我们再来一起了解下。

首先，我们需要明白一点，CA（Certificate Authority，证书颁发机构） 需要验证我们是否拥有该域名，这样才给我们颁发证书。

**文件验证**（HTTP）：CA 将通过访问特定 URL 地址来验证我们是否拥有域名的所有权。因此，我们需要下载给定的验证文件，并上传到您的服务器。

**DNS 验证**：CA 将通过查询 DNS 的 TXT 记录来确定我们对该域名的所有权。我们只需要在域名管理平台将生成的 TXT 记录名与记录值添加到该域名下，等待大约 1 分钟即可验证成功。

所以，如果对服务器操作方便的话，可以选择文件验证；如果对域名的服务器操作比较方便的话，可以选择 DNS 验证。如果两个都方便的话，请随意选啦。

3）**CSR生成**默认为离线生成

**离线生成**、**浏览器生成** 和 **我有 CSR** 又有什么区别呢？来，我们继续了解一下。

**离线生成** <sup>推荐!!!</sup>：私钥在本地加密存储，更安全；公钥自动合成，支持常见证书格式转换，方便部署；支持部分 WebServer 的一键部署，非常便捷。

离线生成的时候，需要先安装 KeyManager，可以提供安全便捷的 SSL 证书申请和管理。下载地址如下：
https://keymanager.org/

Windows 的话，安装的时候要选择“以管理员身份运行”。

**浏览器生成**：在浏览器支持 `Web Cryptography` 的情况下，会使用浏览器根据用户的信息生成 CSR 文件。

>Web Cryptography，网络密码学，用于在 Web 应用程序中执行基本加密操作的 JavaScript API。很多浏览器并不支持

**我有 CSR**：可以粘贴自己的 CSR，然后创建。

**第三步**，选择离线生成，打开 KeyManager

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-4.png)

填写密码后点击「开始」，稍等片刻，出现如下界面。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-5.png)

**第四步**，返回浏览器，点击「下一步」，出现如下界面。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-6.png)

**第五步**，下载文件，并上传至服务器指定目录下。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-7.png)

**第六步**，点击「验证」，通过后，出现以下界面。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-8.png)

**第七步**，点击「保存到KeyManager」，可以看到证书状态变成了已颁发。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-9.png)

### 03、为 Tomcat 配置 jks 格式证书

**第一步**，导出证书。假如服务器选择的 Tomcat，需要导出 Java keystone （简拼为 jks）格式的证书。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-10.png)

注意：私钥的密码在配置 Tomcat 的时候用到。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-11.png)

**第二步**，上传证书至服务器。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-12.png)

**第三步**，配置 Tomcat 的 server.xml。

```xml
 <Connector port="81" protocol="HTTP/1.1"
                        maxThreads="250" maxHttpHeaderSize="8192" acceptCount="100" connectionTimeout="60000" keepAliveTimeout="200000"
                        redirectPort="8443"            
                        useBodyEncodingForURI="true" URIEncoding="UTF-8"  
                        compression="on" compressionMinSize="2048" noCompressionUserAgents="gozilla, traviata"   
            compressableMimeType="text/html,text/xml,application/xml,application/json,text/javascript,application/javascript,text/css,text/plain,text/json,image/png,image/gif"/>

<Connector
  protocol="org.apache.coyote.http11.Http11NioProtocol"
  port="443" maxThreads="200"
  scheme="https" secure="true" SSLEnabled="true"
  keystoreFile="/home/backup/qingmiaokeji.cn.jks" keystorePass="Chenmo"
  clientAuth="false" sslProtocol="TLS"
useBodyEncodingForURI="true" URIEncoding="UTF-8"  
                        compression="on" compressionMinSize="2048" noCompressionUserAgents="gozilla, traviata"   
            compressableMimeType="text/html,text/xml,application/xml,application/json,text/javascript,application/javascript,text/css,text/plain,text/json,image/png,image/gif"
/>
```

其中 keystorePass 为导出证书时私钥的加密密码。

**第四步**，重启 Tomcat，并在浏览器地址栏中输入 `https://itwanger.cn/` 进行测试。

![](http://www.itwanger.com/assets/images/2019/11/https-freessl-13.png)

注意到没，浏览器地址栏前面有一个绿色的安全锁，这说明 HTTPS 配置成功了！好了，为自己鼓个掌！

### 04、最后

你有没有买个五分钟的时间沙漏？如果超过五分钟 HTTPS 还没有配置成功，你过来揍我！




微信搜索「**沉默王二**」公众号，关注后回复「**免费视频**」获取 500G 高质量教学视频（[已分门别类](https://mp.weixin.qq.com/s/GjkEyPW0vgIvuDLYQkBM0A)）。