---
layout: page
title: CA证书与TLS
parent: 技术文档
---

## 1. CA 证书目的

        验证链接的对象身份（保证不是被伪装）

## 2. 术语与对象

### - client

        客户端，访问服务器的资源，需要验证和自己对话的服务器不是被人伪装的

### - server

        提供服务的服务器端，需要证书证明自己的身份

### - CA

        证书授权中心，权威第三方机构，负责颁发证书和验证证书

### - crt 文件

        CA颁发的证书，用来证明身份，包含签发的CA机构，有效时间，域名信息，申请者，申请者公钥等，以及一个签名信息

### - csr 文件

        服务器向CA请求证书时需要提交的信息，包括申请者（服务器）的公钥，组织信息，域名信息等

### - key 文件

        私钥文件

### - pub 文件

        公钥文件

### - pem 文件

        base64编码的文件，单纯用来区分der文件，其内容可以是csr，key，crt中的一个或多个，一般会有`--------BEGIN CERTIFICATE-----`和`-------END CERTIFICATE----`开头和结尾

### - der 文件

        二进制文件，单纯用来区分pem文件

## 3. 请求与使用过程

## 4. 请求证书过程

- server 生成公钥`server.pub`和私钥`server.key`
- server 生成并提交`server.csr`到 CA
- CA 通过其他渠道来验证 csr 中组织者信息，网站信息的正确性与合规性
- CA 通过散列算法计算 csr 的信息，形成`信息摘要`
- CA 使用自己的私钥`ca.key`对`信息摘要`文件进行加密，生成`签名`，记录在`server.crt`中，返回给服务器

## 5. server 和 client 的通信过程

- client 发送自己支持的加密算法和一个随机数`client_random`到 server
- server 选择一个加密算法，附加一个随机数`server_random`和证书`server.crt`返回给 client
- client 通过散列算法计算`server.crt`中的信息，得到`信息摘要`
- client 查找本机中的所有根 CA 信息，是不是有`server.crt`中的 CA，不存在则验证不通过，存在则获取该 CA 的公钥`ca.pub`
- client 通过 CA 的公钥 `ca.pub`对`server.crt`中的`签名`解密，验证是否和上一步的信息摘要一致
- client 验证其他信息，例如访问域名和证书其他信息是否一致
- client 生成随机数字`pre-master`，并用`server_random`、`client_random`和`pre-master`生成协商秘钥`session_key`
- client 用`server.pub`加密`pre-master`，生成`pre-master-encryped`发送到服务器，并告知之后的通信都使用`session_key`进行加密
- server 收到`pre-master-encryped`用`server.key`解密得到`pre-master`，同时用三个随机数生成`session_key`
- server 告知 client 握手结束
