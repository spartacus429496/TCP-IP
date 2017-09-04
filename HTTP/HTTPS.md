## HTTPS

为了确保 Web 安全，在 HTTP 协议基础上改进的 HTTPS。

HTTP 本身不具备加密的功能，HTTP 报文使用明文方式发送。

互联网上的任何角落都存在通信内容被窃听的风险。

HTTP + 加密 + 认证 + 完整性保护 = HTTPS。

HTTPS 通信 ，使用 `https://` 。

HTTPS 就是身披 SSL 外壳的 HTTP，在采用 SSL 后，HTTP 就拥有 HTTPS 的加密、认证和完整性保护功能。

### HTTPS 混合加密机制

HTTPS 采用共享密钥加密和公开密钥加密两者并用的混合加密机制。

* 共享密钥加密(对称密钥加密)：加密和解密使用同一个密钥的方式。以共享密钥方式加密时必须将密钥也发送给对方。
* 公开密钥加密：使用两把密钥，一把公开密钥，可以随意发布，任何人都可以获得；另一把私有密钥，不能让其他任何人知道。
  其原理是发送的一方使用公开密钥加密，接收的一方使用私有密钥解密。
  
在交换密钥环节使用公开密钥加密方式，之后的建立通信交换报文阶段则是使用共享密钥加密方式。

使用数字证书认证机构 CA 来解决公开密钥正确性。

### HTTPS 缺点

* 加密通信会消耗更多的 CPU 和内存资源。
* 要进行 HTTPS 通信，证书必不可少，而使用的证书必须向认证机构 CA 购买。





