
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
```
Host ：api.github.com

Content-Type ：text/plain

Content-Lenth：243
```

##### 响应报文

三部分：状态行，Headers，Body

状态行  HTTP version /status code /status message  比如HTTP/1.1 200 ok

### Request Method 请求方法

##### Get

- 用于获取资源
- 对服务器数据不进行修改
- 不发生body

对应的Retrofit代码
```
@GET(" /users/{id}")
Call<User> getUser(@Path("id") String id, @Query("gender") String gender);
  ##### Post
```
- 用于增加或者修改资源
- 发送给服务器的内容写在body里

对应的Retrofit代码：
```
@FormUrlEncoded
@PUT(" /users/{id}")
Call<User> updateGender( @Path("id") String id, @Field("gender") String
gender) ;
```
##### PUT

- 用于修改资源
- 发送给服务器的内容写在body里面

对应的Retrofit代码：
```
@FormUrlEncoded
@PUT(" /users/{id}")
Call<User> updateGender( @Path("id") String id, @Field("gender") String
gender);
```
##### Delete

- 用于删除资源
- 不发送body

对应的Retrofit代码：
```
@DELETE("/users/{id}")

 Call getUser(@Path("id") String id, @Query("gender") String gender);
```
##### HEAD

- 和GET的使用方法完全相同
- 和FET的唯一区别在于，返回的相应中没有body

## Status Code 状态码

三位数字，⽤于对响应结果做出类型化描述（如「获取成功」「内容未找到」）

- 1xx：临时性消息。如：100 （继续发送）、101（正在切换协议） 。
- 2xx：成功。最典型的是 200（OK）、201（创建成功）。 
- 3xx：重定向。如 301（永久移动）、302（暂时移动）、304（内容未改变）。 
- 4xx：客户端错误。如 400（客户端请求错误）、401（认证失败）、403（被禁⽌）、404（找 不到内容）。 
- 5xx：服务器错误。如 500（服务器内部错误）。

##### Header ⾸部 

作⽤：HTTP 消息的 metadata。

##### Host 

⽬标主机。注意：不是在⽹络上⽤于寻址的，⽽是在⽬标服务器上⽤于定位⼦服务器的。

### Content-Type 

指定 Body 的类型。主要有四类：

1. ##### text/html 

   请求 Web ⻚⾯是返回响应的类型，Body 中返回 html ⽂本。格式如下：
```
HTTP/1.1  200  OK 

Content-Type: text/html; charset=utf-8 

Content-Length: 853   

 <! DOCTYPE html>
<html>

<head>
<meta charset="utf-8">
```
##### 2.x-www-form-urlencoded Web

 ⻚⾯纯⽂本表单的提交⽅式。

name=rengwuxian&gender=male

##### 3.multitype/form-data Web

 ⻚⾯含有⼆进制⽂件时的提交⽅式。

##### 4.application/json , image/jpeg , application/zip ...

 单项内容（⽂本或⾮⽂本都可以），⽤于 Web Api 的响应或者 POST / PUT 的请求



### Content-Length

 指定 Body 的⻓度（字节）。

### Transfer: chunked (分块传输编码 Chunked Transfer Encoding)

⽤于当响应发起时，内容⻓度还没能确定的情况下。和 Content-Length 不同时使⽤。⽤途是尽早给 出响应，减少⽤户等待。

### Location

 指定重定向的⽬标 URL

### User-Agent 

⽤户代理，即是谁实际发送请求、接受响应的，例如⼿机浏览器、某款⼿机 App。

Range / Accept-Range 

###### 按范围取数据 

Accept-Range: bytes 响应报⽂中出现，表示服务器⽀持按字节来取范围数据 

Range: bytes=-<end> 请求报⽂中出现，表示要取哪段数据 

Content-Range:<start>-<end>/total 响应报⽂中出现，表示发送的是哪段数据

作⽤：断点续传、多线程下载。

### Cache

 作⽤：在客户端或中间⽹络节点缓存数据，降低从服务器取数据的频率，以提⾼⽹络性能。



### REST 

REST HTTP 即正确使⽤ HTTP。包括： 

- 使⽤资源的格式来定义 URL 
- 规范地使⽤ method 来定义⽹络请求操作 
- 规范地使⽤ status code 来表示响应状态 
- 其他符合 HTTP 规范的设计准则



## 错题

1. 第四题：请求行由 method path http版本 三部分组成
2. 第八题：post方法用于增加或修改资源，请求报文中含有body
3. 第九题：put不能增加资源，只能修改资源
4. 第十题：delete方法含有body



2020.6.30 22:31


2020.9.9 21:46复习

