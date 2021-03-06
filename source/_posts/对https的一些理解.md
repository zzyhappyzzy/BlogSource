---
title: 对https的一些理解
date: 2017-03-28 16:54:44
tags: ["http","https","ssl/tls","数字签名"]
categories: 随笔
---

随着互联网的普及，不管是PC端的web，还是移动端的app，甚至智能家居等，绝大部分的通信都是依靠http协议传输数据，但http协议只负责传递信息，并不能保证信息的安全性和可靠性。

<!-- more -->
信息安全
===
简单来说，信息安全就是为了保证传递的信息只能被正确的人接收和识别，并且接收方能够验证信息是否被篡改，最终能够正确识别收到的信息。

信息传输的流程：`发送方 -> 传输环境（发送消息） -> 接收方`，为了保证安全性，一般在以下方面做尝试：
* 数据加密/解密
* 专用网络
* 数据真实性验证

有些人可能认为信息安全对我有什么用？根本就没必要关心这些。但真的是这样么？互联网世界，比现实的世界更复杂，到处都可能是陷阱。作为普通的网民，如果你的浏览记录能够被人随意查看，或者被强行插入广告，甚至弹出恶意挂马的链接。。。你会作何感想？作为服务提供方，客户和你们的信息传递被人随意监听，甚至拦截更改数据，你又作何感想？

不管怎么说，服务提供方有义务为用户提供安全的通信机制，来保护用户的隐私和安全。在此基础上，网景公司Netscape提出了SSL`Secure Sockets Layer`协议，之后，国际组织[IETF](https://www.ietf.org/)在SSL的基础上，制定出标准化的TLS`Transport Layer Security`协议，不管是SSL还是TLS，都可用于其他的网络协议，如：FTP、SMTP、NNTP、XMPP等。

HTTPS
===
HTTPS，全称`Hyper Text Transport Protocol Secure`，又被称为`HTTP over TLS`或者`HTTP Secure`，主要目的是在不安全的网络通信环境上建立安全的通信机制，尽量保证信息的安全性和正确性。

SSL/TLS采用非对称加密算法，简单的说，就是公开公钥，保留私钥。通常来说，消息经过公钥加密后，只有持有私钥才能解密，得到最原始的可读消息。反过来，公钥加密的消息，只有通过私钥解密才能还原真实信息。整个过程就是常说的"数字签名"。

数字签名的示意图如下：
![](/secure/digital_signature.png)

但是单纯的数字签名有漏洞，别人可能伪造公钥，诱导用户用假的公钥加密信息，这样它们就能监听你的任意消息。为了避免这种状况，证书颁发机构CA应运而生，它的作用类似公证人，将你的公钥和其它相关的信息，用CA自己的公钥加密后发给你，你再把证书发给用户作为公钥即可证明"你的公钥确实是你的公钥"，而不是别人伪造的。

一般来说，操作系统和浏览器都内嵌了受信任的证书机构，用户无需额外操作。理论上来说，公认的证书颁发机构CA的私钥是不可能泄露的，所以安全方面就不用担心了。但是，证书却是任何人都可以制作和颁发的，所以为了避免上述提到的：伪造公钥的问题。对于用户自己下载的证书，特别是根证书，不要轻易将它加到受信任的证书列表里，可以有效防止个人信息被监听。

http vs https的简要图：
![](/secure/http-vs-https.png)

>mac电脑直接打开Keychain（钥匙串）即可管理证书
>windows用户，参考`https://technet.microsoft.com/zh-cn/library/cc754841(v=ws.11).aspx`

注意
===
https只能提高用户和服务器之间通信的安全性和可信性，并不能提高服务器的安全性。所以，如果服务器自己没有相应的安全防护机制，用户的敏感信息还是有泄露的可能。