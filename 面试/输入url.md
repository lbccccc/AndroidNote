

在浏览器地址栏输入一个URL后回车
https://github.com/lbccccc/AndroidNote/new/master/%E9%9D%A2%E8%AF%95



ssl四次握手（https://www.pianshen.com/article/71241175766/）
一次握手：

步骤 1： 客户端通过发送 Client Hello 报文开始 SSL通信。 报文中包含客户端支持的 SSL的指定版本、 加密组件（Cipher Suite） 列表（所使用的加密算法及密钥长度等） 。

二次握手：

步骤 2： 服务器可进行 SSL通信时， 会以 Server Hello 报文作为应答。 和客户端一样， 在报文中包含 SSL版本以及加密组件。 服务器的加密组件内容是从接收到的客户端加密组件内筛选出来的。

步骤 3： 之后服务器发送 Certificate 报文。 报文中包含公开密钥证书。

步骤 4： 最后服务器发送 Server Hello Done 报文通知客户端， 最初阶段的 SSL握手协商部分结束。

三次握手：

步骤 5： 客户端以 Client Key Exchange 报文作为回应。 报文中包含通信加密中使用的一种被称为 Pre-mastersecret 的随机密码串。 该报文已用步骤 3 中的公开密钥进行加密。

步骤 6： 接着客户端继续发送 Change Cipher Spec 报文。 该报文会提示服务器， 在此报文之后的通信会采用Pre-master secret 密钥加密。

步骤 7： 客户端发送 Finished 报文。 该报文包含连接至今全部报文的整体校验值。 这次握手协商是否能够成功， 要以服务器是否能够正确解密该报文作为判定标准。

四次握手：

步骤 8： 服务器同样发送 Change Cipher Spec 报文。

步骤 9： 服务器同样发送 Finished 报文。


2020 10.21 21.54
