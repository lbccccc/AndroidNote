
## Retrofit使用方法简介

1.创建一个interface作为Web Service的请求集合，在里面用注解写入需要配置的请求方法

```
public interface GitHubService {
 @GET("users/{user}/repos")
 Call<List<Repo>> listRepos(@Path("user") String user);
}
```

2.使用Retrofit创建出interface的实例

```
Retrofit retrofit = new Retrofit.Builder() .baseUrl("https://api.github.com/") .build(); GitHubService service = retrofit.create(GitHubService.class);
```

3.调用创建出的Service实例的对应方法，创建出相应的可以用来发起网络请求的Call对象

```
Call<List<Repo>> repos = service.listRepos("octocat");
```

4.使用Call.execute()  （同步）,或者Call.enqueue()   （异步）来发起请求

```
repos.enqueue(callback)
```

# Retrofit 源码结构总结

- 通过Retrofit.create(Class)方法创建出Service interface 的实例，然后Service 的方法就可以用了，这是Retrofit 代码结构的核心
- Retrofit. create()  方法内部，使用的是Proxy . newProxyInstance()方法来创建
  Service实例。这个方法会为参数中的多个interface (具体对于 Retrofit来说，是固定传入一个
  interface)创建一个对象，这个对象实现了所有interface的每个方法，并且每个方法的实现都
  是雷同的:调用对象实例内部的-个InvocationHandler 成员变量的invoke() 方法，并
  把自己的方法信息传递进去。这样就在实质上实现了代理逻辑: interface 中的方法全部由一个
  另外设定的
  InvocationHandler对象来进行代理操作。并且，这些方法的具体实现是在运行
  时生成interface实例时才确定的，而不是在编译时(虽 然在编译时就已经可以通过代码逻辑推
  断出来)。这就是网.上所说的「动态代理机制」的具体含义。
- 

●因此， invoke() 方法中的逻辑，就是Retrofit创建Service实例的关键。这个方法内有三行
关键代码，共同组成了具体逻辑: .

1.ServiceMethod 的创建:

```
ServiceMethod<Object, Object> serviceMethod =
 (ServiceMethod<Object, Object>) loadServiceMethod(method);
```

这行代码负责读取interface中原方法的信息(包括返回值类型、方法注解、参数类型、参数注
解)，并将这些信息做初步分析。

2.OkHttpCall 的创建:

```OkHttpCall<0bject> okHttpCall = new OkHttpCall<> ( serviceMethod, args ) ;```
okHttpCall是retrofit2.call 的子类。这行代码负责将ServiceMethod封装进一个retrofit2.Call对象;而这个对象可以在需要的时候(例如它的engueue() 方法被调用的时候，利用ServiceMethod中包含的信息来创建一个okhttp3.Call 对象，并调用这个okhttp3.Call对象来进行网络请求的发起，然后对结果进行预处理(如类型转换)。

3.adapt() 万法:
```return serviceMethod. adapt ( okHttpCall);```
这个方法会使用ServiceMethod中的callAdapter对象来把okHttpCall对象进行转换，生成一个新的retrofit2.call对象，在这个新的Call 对象中，后台线程发起的请求，会在相应返回后，从主线程中调用回调方法，实现线程的自动切换。
另外，这个方法不止可以生成新的retrofit2.call 对象,也可以生成别的类型对象，例如RxJava的Obervable ，来让Retrofit可以和RxJava结合使用。

（多理解，还是没看太懂）

2020.7.2 17:26
