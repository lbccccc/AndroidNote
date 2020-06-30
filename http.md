
## HTTP的定义

一种网络传输协议，位于TCP / IP协议族的最顶层- -- 应用层

### HTTP

Hypertext Transfer Protocol,超文本传输协议，和HTML (Hypertext Markup Language超文本标
记语言)一起诞生，用于在网络上请求和传输HTML内容。

超文本，即「扩展型文本」，指的是HTML中可以有链向别的文本的链接(hyperlink)

### HTTP的工作方式

##### 浏览器

用户在输入地址之后回车或者点击链接，然后浏览器会拼装HTTP报文并发送请求给服务器，然后服务区处理请求后会发送响应报文给浏览器，然后浏览器再解析响应报文再使用渲染引擎显示到界面

##### 手机APP

用户点击或者界面自动触发联网需求，然后Android代码调用拼装HTTP报文并发送请求到服务器，然后服务器处理请求之后再发送响应报文给手机，最后Android代码会处理响应报文并作出相应的处理比如数据存储，显示数据到界面等等

## URL和HTTP报文

#### URL格式

三部分：协议类型，服务器地址（和端口号），路径

格式为 协议类型://服务器地址[:端口号]路径

http://hencoder.com/user?gender=male

#### 报文格式

##### 请求报文

三部分：请求行，Headers，Body

请求行的格式为     方法/路径/HTTP version  比如 GET/users HTTP/1.1

Headers 也分为三部分  Host ，Content-Type ，Content-Lenth  比如  

Host ：api.github.com

Content-Type ：text/plain

Content-Lenth：243

##### 响应报文

三部分：状态行，Headers，Body

状态行  HTTP version /status code /status message  比如HTTP/1.1 200 ok
