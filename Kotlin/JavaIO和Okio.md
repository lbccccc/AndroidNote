# Java I/O 和 Okio

## I/O

- I/O 是什么？ 

  - 程序内部和外部进⾏数据交互的过程，就叫输⼊输出。

    - 程序内部是谁？内存
    - 程序外部是谁？
      - ⼀般来说是两类：本地⽂件和⽹络。 
      - 也有别的情况，⽐如你和别的程序做交互，和你交互的程序也属于外部，但⼀般 来说，就是⽂件和⽹络这么两种。 

    - 从⽂件⾥或者从⽹络上读数据到内存⾥，就叫输⼊；从内存⾥写到⽂件⾥或者发送到 ⽹络上，就叫输出

  - Java I/O 作⽤只有⼀个：和外界做数据交互

- 用法

  - 使⽤流，例如 FileInputStream / FileOutputStream

  - 可以⽤ Reader 和 Writer 来对字符进⾏读写

  - 流的外⾯还可以套别的流，层层嵌套都可以 BufferedXXXX 可以给流加上缓冲。对于输⼊流，是每次多读⼀些放在内存⾥⾯，下次再去 数据就不⽤再和外部做交互（即不必做 IO 操作）；对于输出流，是把数据先在内存⾥⾯攒 ⼀下，攒够⼀波了再往外部去写。

    通过缓存的⽅式减少和和外部的交互，从⽽可以提⾼效率 

  - ⽂件的关闭：close() 

  - 需要⽤到的写过的数据，flush() ⼀下可以保证数据真正写到外部去（读数据没有这样的担 忧）

  - 这个就是 Java 的 I/O，它的原理就是内存和外界的交互 

    - Java I/O 涉及的类⾮常多，但你⽤到哪个再去关注它就⾏了，不要背类的继承关系图

  - 使⽤ Socket 和 ServerSocket 进⾏⽹络交互

    ```
    try {
    ServerSocket serverSocket = new ServerSocket( r8080);
    Socket socket = serve rSocket。accept();
    BufferedReader reader = new Buf feredReader(new InputStreamReader(socket. getInputStream()));
    BufferedWriter writer = new BufferedWriter(new OutputStreamWriter( socket . getOutputStream()));
    String data;
    while ((data = reader. readLine()) != null) {
    writer .write(data);
    writer . write(System. lineSeparator());
    writer. flush();
    }
    } catch (IOException e) {
    e. printStackTrace();
    }
    ```

## NIO

- NIO 和 IO 的区别有⼏点：

  1. 传统 IO ⽤的是插管道的⽅式，⽤的是 Stream；NIO ⽤的也是插管道的⽅式，⽤的是 Channel。
     - NIO 的 Channel 是双向的
  2. NIO 也⽤到 buffer 
     - 它的 Buffer 可以被操作 
     - 它强制使⽤ Buffer 
     - 它的 buffer 不好⽤
  3. NIO 有⾮阻塞式的⽀持
     -  只是⽀持⾮阻塞式，⽽不是全是⾮阻塞式。默认是阻塞式的 
     - ⽽且就算是⾮阻塞式，也只是⽹络交互⽀持，⽂件交互是不⽀持的

- 使⽤： 

  - NIO 的 Buffer 模型：

  1. position：用于记录buffer中当前的位置，感觉有点类似数组的下标
  2. limit：最多可以读或者写到buffer的某个位置，最大值
  3. capacity：buffer的大小

  - ⽤ NIO 来读⽂件的写法：

    - 使⽤ fine.getChannel() 获取到 Channel 
    - 然后创建⼀个 Buffer 
    - 再⽤ channel.read(buffer)，把⽂件内容读进去 
    - 读完以后，⽤ flip() 翻⻚ 
    - 开始使⽤ Buffer 
    - 使⽤完之后记得 clear() ⼀下

    ```
    try (RandomAccessFile file = new RandomAccessFile( name: "./26/new_ text. txt",I mode "r");
    FileChannel channel = file . getChannel()) {
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    channel. read(byteBuffer);
    byteBuffer.flip();
    Sys tem. out . print Ln( Charset . de faul tCharset() . decode(byteBuffer));
    byteBuffer .clear();
    } catch (FileNotFoundException e) {
    e. printStackTrace();
    } catch (IOException e) {
    e. printStackTrace();
    }
    ```

# Okio

特点：

- 它也是基于插管的，⽽且是单向的，输⼊源叫 Source，输出⽬标叫 Sink 
- ⽀持 Buffer 
  - 向 NIO ⼀样，可以对 Buffer 进⾏操作 
  - 但不强制使⽤ Buffer

⽤法：

- 输⼊：

  ```
  try (BufferedSource source = Okio. buffer(Okio. source(new File( pathname:”./26/text. txt")))) {
  System . out . print Ln( source. readUtf8Line());
  catch (IOException e) {
  e. printStackTrace();
  }
  ```

- 输出进去之后，还可以把它当做输⼊源来取数据：

  ```
  Buffer buffer = new Buffer();
  0bj ect0utputStream obj ectOutputStream = new 0bj ect0utputSt ream( buffer . outputStream());
  obj ectOutputSt ream . writeUTF ( str "abab");
  obj ectOutputSt ream . writeBoolean( val true) ;
  obj ectOutputStream.writeChar( val: 'b');
  obj ect0utputStream. flush();
  0bj ectInputSt ream objectInputStream = new 0bj ectInputSt ream buffer . inputStream());
  System. out . print ln(objectInputSt ream. readUTF());
  System. out. print ln(obj ectInputSt ream. readBoo lean());
  ```

  

2020 7.20 16:27
