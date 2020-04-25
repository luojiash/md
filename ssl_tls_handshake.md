# SSL/TLS 握手过程

SSL（Security Socket Layer）是TLS（Transport Layer Security）的前身。TLS运行在TCP之上，所以握手过程首先要经过三次握手。
完成TCP握手后客户端和服务器开始建立加密通道：

1. Client

- Client Hello，发送客户端支持的TLS协议版本和加密算法，并生成一个随机数

2. Server

- Server Hello，选择使用的协议版本和加密算法，生成一个随机数
- Certificate，发送CA证书
- Server Key Exchange，发送 DH parameter 和签名（用于密钥交换）
- Server Hello Done

3. Client

- Client Key Exchange，发送DH paramter
- Change Cipher Spec，确认使用的协议版本和加密算法

4. Server

- Change Cipher Spec，确认使用的加密算法和密钥

以上步骤描述了使用DH密钥交换的TLS连接的建立过程，使用DH算法传输第三个随机数。可以看出建立TLS连接至少要两次往返时间。

另一种密钥交换算法是RSA密钥交换，Client使用Server公钥加密第三个随机数，发送给Server，Client
和Server使用相同的三个随机数生成对称密钥。这种密钥交换算法没有前向安全性，因为私钥泄露后
可以破解以前的加密流量。

[SSL/TLS协议运行机制的概述 - 阮一峰的网络日志](http://www.ruanyifeng.com/blog/2014/02/ssl_tls.html)  
[三种解密 HTTPS 流量的方法介绍 | JerryQu 的小站](https://imququ.com/post/how-to-decrypt-https.html)
