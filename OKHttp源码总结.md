# OkHttp

##  OkHttp 使⽤⽅法简介

1. 创建⼀个 OkHttp 的实例

```
OkHttpClient client = new OkHttpClient.Builder().build();
```

2. 创建 Request 

   ```
   Request request = new Request.Builder()
   .url("http://hencoder.com")
   .build();
   ```

   

3. 创建 Call 并发起⽹络请求

   ```
   client.newCall(request).enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
    }
    @Override
    public void onResponse(Call call, Response response) throws IOException
   {
   Log.d("okhttp response", response.body().string());
    }
   });
   ```

   

   ##  OkHttp 源码总结

    OkHttpClient 相当于配置中⼼，所有的请求都会共享这些配置（例如出错是否重试、共享的 连接池）。 OkHttpClient 中的配置主要有：

   -  Dispatcher dispatcher ：调度器，⽤于调度后台发起的⽹络请求，有后台总请求数和 单主机总请求数的控制。

   -  List protocols ：⽀持的应⽤层协议，即 HTTP/1.1、HTTP/2 等。

   -  List connectionSpecs ：应⽤层⽀持的 Socket 设置，即使⽤明⽂ 传输（⽤于 HTTP）还是某个版本的 TLS（⽤于 HTTPS）。List interceptors ：⼤多数时候使⽤的 Interceptor 都应该配置到这  ⾥。

   -  List networkInterceptors ：直接和⽹络请求交互的 Interceptor 配 置到这⾥，例如如果你想查看返回的 301 报⽂或者未解压的 Response Body，需要在这⾥ 看。

   -  CookieJar cookieJar ：管理 Cookie 的控制器。OkHttp 提供了 Cookie 存取的判断⽀ 持（即什么时候需要存 Cookie，什么时候需要读取 Cookie，但没有给出具体的存取实现。 如果需要存取 Cookie，你得⾃⼰写实现，例如⽤ Map 存在内存⾥，或者⽤别的⽅式存在 本地存储或者数据库。

   -  Cache cache ：Cache 存储的配置。默认是没有，如果需要⽤，得⾃⼰配置出 Cache 存 储的⽂件位置以及存储空间上限。

   -  HostnameVerifier hostnameVerifier ：⽤于验证 HTTPS 握⼿过程中下载到的证书所 属者是否和⾃⼰要访问的主机名⼀致。

   - #####  CertificatePinner certificatePinner ：⽤于设置 HTTPS 握⼿过程中针对某个 Host 的 Certificate Public Key Pinner，即把⽹站证书链中的每⼀个证书公钥直接拿来提前 配置进 OkHttpClient ⾥去，以跳过本地根证书，直接从代码⾥进⾏认证。这种⽤法⽐较少 ⻅，⼀般⽤于防⽌⽹站证书被⼈仿制。(这个很重要，可以用于自签名证书，很方便)

   -  Authenticator authenticator ：⽤于⾃动重新认证。配置之后，在请求收到 401 状 态码的响应是，会直接调⽤ authenticator ，⼿动加⼊ Authorization header 之后 ⾃动重新发起请求。

   -  boolean followRedirects ：遇到重定向的要求是，是否⾃动 follow。

   -  boolean followSslRedirects 在重定向时，如果原先请求的是 http ⽽重定向的⽬标是 https，或者原先请求的是 https ⽽重定向的⽬标是 http，是否依然⾃动 follow。（记得， 不是「是否⾃动 follow HTTPS URL 重定向的意思，⽽是是否⾃动 follow 在 HTTP 和 HTTPS 之间切换的重定向）

   -  boolean retryOnConnectionFailure ：在请求失败的时候是否⾃动重试。注意，⼤多 数的请求失败并不属于 OkHttp 所定义的「需要重试」，这种重试只适⽤于「同⼀个域名的 多个 IP 切换重试」「Socket 失效重试」等情况。

   -  int connectTimeout ：建⽴连接（TCP 或 TLS）的超时时间。

   -  int readTimeout ：发起请求到读到响应数据的超时时间。

   -  int writeTimeout ：发起请求并被⽬标服务器接受的超时时间。（为什么？因为有时候 对⽅服务器可能由于某种原因⽽不读取你的 Request）

   

- newCall(Request) ⽅法会返回⼀个 RealCall 对象，它是 Call 接⼝的实现。当调⽤ RealCall.execute() 的时候， RealCall.getResponseWithInterceptorChain() 会被 调⽤，它会发起⽹络请求并拿到返回的响应，装进⼀个 Response 对象并作为返回值返回； RealCall.enqueue() 被调⽤的时候⼤同⼩异，区别在于 enqueue() 会使⽤ Dispatcher 的线程池来把请求放在后台线程进⾏，但实质上使⽤的同样也是 getResponseWithInterceptorChain() ⽅法。

- getResponseWithInterceptorChain() ⽅法做的事：把所有配置好的 Interceptor 放在 ⼀个 List ⾥，然后作为参数，创建⼀个 RealInterceptorChain 对象，并调⽤ chain.proceed(request) 来发起请求和获取响应。

- 在 RealInterceptorChain 中，多个 Interceptor 会依次调⽤⾃⼰的 intercept() ⽅ 法。这个⽅法会做三件事：

  1. 对请求进⾏预处理 

  2. 预处理之后，重新调⽤ RealIntercepterChain.proceed() 把请求交给下⼀个 Interceptor 扔物线学堂 rengwuxian.com2 

  3. 在下⼀个 Interceptor 处理完成并返回之后，拿到 Response 进⾏后续处理 

     

     ###### 当然了，最后⼀个 Interceptor 的任务只有⼀个：做真正的⽹络请求并拿到响应



- 从上到下，每级 Interceptor 做的事：

  -  ⾸先是开发者使⽤ addInterceptor(Interceptor) 所设置的，它们会按照开发者的要 求，在所有其他 Interceptor 处理之前，进⾏最早的预处理⼯作，以及在收到 Response 之后，做最后的善后⼯作。如果你有统⼀的 header 要添加，可以在这⾥设置；
  -  然后是 RetryAndFollowUpInterceptor ：它负责在请求失败时的重试，以及重定向的 ⾃动后续请求。它的存在，可以让重试和重定向对于开发者是⽆感知的；
  -  BridgeInterceptor ：它负责⼀些不影响开发者开发，但影响 HTTP 交互的⼀些额外预 处理。例如，Content-Length 的计算和添加、gzip 的⽀持（Accept-Encoding: gzip）、 gzip 压缩数据的解包，都是发⽣在这⾥； 
  - CacheInterceptor ：它负责 Cache 的处理。把它放在后⾯的⽹络交互相关 Interceptor 的前⾯的好处是，如果本地有了可⽤的 Cache，⼀个请求可以在没有发⽣ 实质⽹络交互的情况下就返回缓存结果，⽽完全不需要开发者做出任何的额外⼯作，让 Cache 更加⽆感知；
  -  ConnectInterceptor ：它负责建⽴连接。在这⾥，OkHttp 会创建出⽹络请求所需要的 TCP 连接（如果是 HTTP），或者是建⽴在 TCP 连接之上的 TLS 连接（如果是 HTTPS）， 并且会创建出对应的 HttpCodec 对象（⽤于编码解码 HTTP 请求）；
  -  然后是开发者使⽤ addNetworkInterceptor(Interceptor) 所设置的，它们的⾏为逻 辑和使⽤ addInterceptor(Interceptor) 创建的⼀样，但由于位置不同，所以这⾥创 建的 Interceptor 会看到每个请求和响应的数据（包括重定向以及重试的⼀些中间请求 和响应），并且看到的是完整原始数据，⽽不是没有加 Content-Length 的请求数据，或者 Body 还没有被 gzip 解压的响应数据。多数情况，这个⽅法不需要被使⽤；
  -  CallServerInterceptor ：它负责实质的请求与响应的 I/O 操作，即往 Socket ⾥写⼊请 求数据，和从 Socket ⾥读取响应数据。

  好复杂，和上一节课一样看不太懂。以后再回来看

  2020.7.3 0:24
