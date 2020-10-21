https://www.zhihu.com/question/302412059

# TCL

SSL最初只是用于加密HTTP的，在SSL3.0版本的基础上重新设计并命名叫做TLS

TCL最开始应该是四次握手

TCL1.2开始3次握手

TCL1.3两次握手     减少的都是后面两次



# HTTP1

服务器推给浏览器的页面通常会嵌入很多图片资源啥的，以及动态页面资源链接，或者是第三方网站的链接资源。浏览器还需要根据这些文本链接的内容去寻找对应的服务器，继续下载链接所对应的内容。

浏览器通常的流程是重新创建一个TCP连接，TCL连接，HTTP交易。





# HTTP2

https://www.zhihu.com/search?type=content&q=HTTP2%E5%A4%9A%E8%B7%AF%E5%A4%8D%E7%94%A8

HTTP2把要传输的信息分割成一个个二进制帧，首部信息会被封装到HEADER Frame

HTTP/2设计是基于“二进制帧”进行设计的。在这里有一个副作用，多个HTTP流使用同一个TCP连接，，如果第一个HTTP流遭遇了拥塞，剩下的HTTP流也没法发出去，这就是头部阻塞



# QUIC

https://www.cnblogs.com/amyzhu/p/8285300.html

只需要两次握手，1RTT就可以建立

QUIC同时复用了HTTP2.0的多路复用（Multiplexing）功能，但由于QUIC基于UDP，避免了HTTP/2的Head-of-Line Bolcking问题.

有和TCP拥塞控制差不多的算法



# HTTP3

将QUIC和HTTP分离







2020 10.21  23.49
