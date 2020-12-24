# NIO简介

全称 Java New IO，用于取代Java IO，NIO与IO有着相同的作用，但操作方式不同。NIO支持面向缓冲区和通道的IO操作，比IO操作文件读写更高效。

1. NIO与IO的区别

   - IO是面向流的，流是单向的。
   - NIO是面向缓冲区的，缓冲区是双向的。

   | IO     | NIO             |
   | ------ | --------------- |
   | 面向流 | 面向缓冲区      |
   | 阻塞IO | 非阻塞IO        |
   | 无     | selector 选择器 |

   

2. NIO图解

   ![image-20201118113005596](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118113005596.png)

   **通道** 好比火车轨道，轨道（通道）本身只是**连接**两个地方，不能**载人载物**，但火车（**缓冲区**）可以。所以说通道必须配合缓冲去来使用。

   若要使用NIO系统，首先要建立连接，以及用于容纳数据的缓冲区，然后操作缓冲区传送数据。

   而IO流 好比自来水管，它直接就能将水（数据）送到目的地。

# 缓冲区Buffer

1. 分类

   缓冲区的底层就是一个数组，它一共有7种，基本类型中除了boolean以外都有对应的缓冲区，但最常用的还是ByteBuffer。

   - ByteBuffer：最常用
   - IntBuffer
   - ...

2. 缓冲区的四个核心属性

   - **capacity**：缓冲区最大存储数据的容量，一旦声明不能修改。
   - **limit**：缓冲区可操作数据的大小，limit之后的数据不能读写。
   - **position**：缓冲区中正在操作的位置。
   - **mark**：标记当前position的位置，通过reset方法将position重置到标记位置
   - 大小关系： 0 <= mark <= position <= limit <= capacity

3. 缓冲区的简单使用

   ```java
   ByteBuffer buffer = ByteBuffer.allocate(1024);
   System.out.println("------缓冲区初始化后-----");
   System.out.println("position:"+buffer.position()+"，limit:"+buffer.limit()); // position:0，limit:1024
   String str = "abcde";
   buffer.put(str.getBytes());
   System.out.println("-----缓冲区添加数据后------");
   System.out.println("position:"+buffer.position()+"，limit:"+buffer.limit());	// position:5，limit:1024
   //切换到读模式
   buffer.flip();
   System.out.println("-----缓冲区转换到读模式后------");
   System.out.println("position:"+buffer.position()+"，limit:"+buffer.limit());//position:0，limit:5
   //将缓冲区内容复制到字节数组中
   byte[] bytes = new byte[buffer.limit()];
   buffer.get(bytes);
   System.out.println("-----缓冲区将数据传输出去后------");
   System.out.println("position:"+buffer.position()+"，limit:"+buffer.limit());//position:5，limit:5
   System.out.println("-----缓冲区中存储的内容------");
   System.out.println(new String(bytes,0,bytes.length));	//abcde
   buffer.rewind();
   System.out.println("-----重读缓冲区中的数据------");
   System.out.println("position:"+buffer.position()+"，limit:"+buffer.limit());	//position:0，limit:5
   buffer.clear();
   System.out.println("-----将缓冲区清空------"); //只是将position,limit重置，缓冲区数据还在
   System.out.println("position:"+buffer.position()+"，limit:"+buffer.limit());//position:0，limit:1024
   System.out.println("-----将缓冲区清空后继续读------");
   System.out.println((char)buffer.get(0));	//a
   ```

4. 字节缓冲区常用方法

   - allocate：分配缓冲区，单位为字节
   - put：往缓冲区中存储数据，参数为字节数组
   - filp：缓冲区转换为读状态
   - get：将缓冲区内容存储到字节数组中，参数为存储结果的字节数组
   - rewind：重读，将position设为0
   - clear：重置position,limit
   - mark：标记当前position位置
   - reset：将position重置到标记位置
   - hasRemaining：缓冲区中是否还有内容
   - remaing：返回缓冲区中还可以读的字节数



## 非直接缓冲区

使用allocate方法生成的缓冲区。

缺点：缓冲区中存储的数据需要频繁的在内核地址空间和用户地址空间复制粘贴。

优点：安全，缓冲区存储的数据jvm可以控制。

图解：

![image-20201118123528482](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118123528482.png)





## 直接缓冲区

使用allocateDirect方法生成的缓冲区。

优点：省略了频繁的复制粘贴，直接在内存中开辟了一块地方用来存储缓冲区的数据，jvm和物理磁盘只需要通过这块地方就可以传递数据。

缺点：不安全，内存中的这块地方不受jvm控制。

​			消耗资源大，内存中的地址本来大家都可以用，现在只能让缓冲区用。

图解：

![image-20201118123608098](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118123608098.png)

# 通道

通道在创建时连着目的地或出发地。

## 计算机传输数据的演变

1. CPU 全权控制 IO接口：

   ​	CPU控制IO接口，完成传输数据的操作。当IO请求很多时，CPU就无法处理其他任务。

2. DMA 控制 IO接口：

   ​	IO请求首先发送给DMA，DMA收到请求后向CPU申请权限，申请成功之后就可以控制系统总线，完成IO接口传输数据的操作，而CPU就可以处理其他任务。但DMA采用总线的控制方式，当IO请求很多时就会在总线上发生冲突（总线同一时刻只能由一个设备控制）。

3. 通道控制IO接口

   原来DMA通过总线控制IO接口，现在由通道来控制。

   通道 是一个独立的处理器，也就是说DMA不再需要向CPU申请权限即可处理IO请求。

   ![image-20201118130254613](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118130254613.png)

## 通道

1. 通道的主要实现类

   java.nio.channels.Channel 接口

   - FileChannel：用来操作本地文件的传输
   - SocketChannel：
   - ServerSocketChannel：这两个用来操作网络NIO的TCP部分
   - DatagramChannel：操作网络NIO的 UDP 部分

2. 获取通道的三种方法

   - Java对支持通道的类提供了 getChannel() 方法
     - 本地 IO
       - FileInputStream / FileOutputStream
       - RandomAccessFile
     - 网络 IO
       - Socket
       - ServerSocket
       - DatagramSocket
   - jdk 1.7 中对各个通道提供了静态方法 open()
   - jdk 1.7 中 Files工具类 的 newByteChannel() 方法

3. 演示

   1. 使用非直接缓冲区复制粘贴图片

      ```java
      FileInputStream fileInputStream = new FileInputStream("zhaopian.jpg");
      FileOutputStream fileOutputStream = new FileOutputStream("zhaopian_copy.jpg");
      FileChannel inputChannel = fileInputStream.getChannel();
      FileChannel OutputChannel = fileOutputStream.getChannel();
      ByteBuffer buffer = ByteBuffer.allocate(1024);
      while (inputChannel.read(buffer) != -1){
          buffer.flip();
          OutputChannel.write(buffer);
          buffer.clear();
      }
      fileInputStream.close();
      fileOutputStream.close();
      inputChannel.close();
      OutputChannel.close();
      ```

      

   2. 使用直接缓冲区复制粘贴图片

      ```java
      //1.先获取通道
      FileChannel inchannel = FileChannel.open(Paths.get("zhaopian.jpg"), StandardOpenOption.READ);
      FileChannel outchannel = FileChannel.open(Paths.get("zhaopian1.jpg"), StandardOpenOption.WRITE, StandardOpenOption.READ, StandardOpenOption.CREATE);
      //2.创建内存映射文件
      MappedByteBuffer inMapBuff = inchannel.map(FileChannel.MapMode.READ_ONLY, 0, inchannel.size());
      MappedByteBuffer outMapBuff = outchannel.map(FileChannel.MapMode.READ_WRITE, 0, inchannel.size());
      //3.直接对缓冲区进行数据的读写操作
      byte[] bytes = new byte[inMapBuff.limit()];
      inMapBuff.get(bytes);
      outMapBuff.put(bytes);
      //4.关闭通道
      inchannel.close();
      outchannel.close();
      ```

## 通道之间的传输

1. 通道之间传输数据（利用的是内存映射文件传输）

   ```java
   //1.先获取通道
   FileChannel inchannel = FileChannel.open(Paths.get("zhaopian.jpg"), StandardOpenOption.READ);
   FileChannel outchannel = FileChannel.open(Paths.get("zhaopian2.jpg"), StandardOpenOption.WRITE, StandardOpenOption.READ, StandardOpenOption.CREATE);
   //2.管道之间传输数据
   inchannel.transferTo(0,inchannel.size(),outchannel);
   //3.关闭通道
   inchannel.close();
   outchannel.close();
   ```

## 分散与聚集

1. 分散读取：

![image-20201118164745202](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118164745202.png)

2. 聚集写入：依次从缓冲区中读取数据将通道填满

   ![image-20201118164927892](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118164927892.png)

3. 演示

   ```java
   RandomAccessFile randomAccessFile = new RandomAccessFile("zhaopian.jpg","rw");
   FileChannel inchannel = randomAccessFile.getChannel();
   ByteBuffer buffer1 = ByteBuffer.allocate(100);
   ByteBuffer buffer2 = ByteBuffer.allocate(200);
   ByteBuffer[] byteBuffers = {buffer1,buffer2};
   inchannel.read(byteBuffers);
   for (ByteBuffer bf : byteBuffers){
       bf.flip();
   }
   System.out.println(new String(byteBuffers[0].array(),buffer1.limit()));
   System.out.println(new String(byteBuffers[1].array(),buffer2.limit()));
   System.out.println("------分散读取↑---聚集写入↓----");
   RandomAccessFile rw = new RandomAccessFile("zhaopian3.jpg", "rw");
   FileChannel outchannel = rw.getChannel();
   outchannel.write(byteBuffers);
   
   inchannel.close();
   outchannel.close();
   ```

   

## 字符缓冲区

### 字符集Charset

编码：字符串 --->  **字节数组**

解码：**字节数组**----> 字符串

字节数组才是最终存储对象，所以是编成字节数组和解字节数组。

演示：

```java
//1.获取字符集对象
Charset gbk = Charset.forName("GBK");
//2.获取对应的编码器和解码器
CharsetEncoder charsetEncoder = gbk.newEncoder();
CharsetDecoder charsetDecoder = gbk.newDecoder();
//3.获取字符型缓冲区，添加数据，变读模式
CharBuffer charBuffer = CharBuffer.allocate(1024);
charBuffer.put("我要变强！！！！");
charBuffer.flip();
//4.将字符缓冲区编码称字节缓冲区
ByteBuffer encode = charsetEncoder.encode(charBuffer);
//5.输出编码后的字节缓冲区
System.out.println(new String(encode.array(),encode.limit()));
System.out.println("-----编码↑----解码↓----");
//1.将字节缓冲区解码
CharBuffer decode = charsetDecoder.decode(encode);
decode.flip();
System.out.println(new String(decode.array()));
```



# 阻塞式IO与非阻塞式NIO

## 阻塞式IO

传统的网络通信，使用的是阻塞式IO。图解如下：

![image-20201118190638549](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118190638549.png)

客户端向服务端发送读写请求时，如果服务端不能判断请求是否有效时就会将该请求阻塞，直到服务端判断有效时才相应请求。为了解决这个问题，服务端为每一个网络IO请求分配一个线程来接待他，直到判断请求有效后再处理请求。虽然缓解了客户端请求的阻塞问题，但服务端效率并没有大幅度提高，所以引入了NIO的非阻塞式请求。

代码：

```java
public void clientSocket() throws IOException {
    //1.建立socket通道，需要服务器地址和端口号
    SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 9898));
    //2.创建传输文件的通道
    FileChannel fileChannel = FileChannel.open(Paths.get("zhaopian.jpg"), StandardOpenOption.READ);
    //3.将文件通道通过缓冲区发送给socket通道
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    while ((fileChannel.read(byteBuffer)) != -1){
        byteBuffer.flip();
        socketChannel.write(byteBuffer);
        byteBuffer.clear();
    }
    //4.告诉服务器socket通道传送完了
     socketChannel.shutdownOutput();
     //5.从socket通道中读服务端发来的数据
        int len = 0;
        while ((len = socketChannel.read(byteBuffer)) != -1){
            byteBuffer.flip();
            System.out.println(new String(byteBuffer.array(),0,len));
            byteBuffer.clear();
        }
    //6.关闭通道
    socketChannel.close();
    fileChannel.close();
}

public void serverSocket() throws IOException {
    //1.建立服务端socket,然后绑定端口号
    ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
    serverSocketChannel.bind(new InetSocketAddress(9898));
    //2.创建文件通道
    FileChannel fileChannel = FileChannel.open(Paths.get("zhaopian4.jpg"), StandardOpenOption.READ, StandardOpenOption.WRITE, StandardOpenOption.CREATE);
    //3.服务端socket接受请求
    SocketChannel accept = serverSocketChannel.accept();
    //4.读取服务端socket通道内容
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    while ((accept.read(byteBuffer)) != -1){
        byteBuffer.flip();
        fileChannel.write(byteBuffer);
        byteBuffer.clear();
    }
    //5.向服务端socket通道中写数据
        byteBuffer.put("服务端收到了请求".getBytes());
        byteBuffer.flip();
        accept.write(byteBuffer);
    
    //6.关闭通道
    serverSocketChannel.close();
    accept.close();
    fileChannel.close();
}
```



## 非阻塞式NIO

在客户端和服务端设置一个selector选择器，将那些准备好的通道发送给服务端去处理，起到一个筛选的作用。同时它还可以监视这些管道。

![image-20201118191504752](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118191504752.png)

### 阻塞时NIO的三大核心

1. 通道：负责连接

   java.nio.channels.Channel 接口：

   - SelectableChannel
     - SocketChannel
     -  ServerSocketChannel
     - DatagramChannel
     - Pipe.SinkChannel
     - Pipe.SourceChannel

2. 缓冲区：负责数据的存取。

3. 选择器：是SelectableChannel 的多路复用器，用于监控SelectableChannel 的 IO 状况。



SelectionKey

![image-20201118211955968](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118211955968.png)



![image-20201118212100600](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118212100600.png)



### SocketChannel

代码：

```java
public void client() throws IOException {
    //1.创建socket通道
    SocketChannel socketChannel = SocketChannel.open(new InetSocketAddress("127.0.0.1", 9898));
    //2.设置为非阻塞模式
    socketChannel.configureBlocking(false);
    //3.用缓冲区往socket通道中传送数据
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    byteBuffer.put("客户端nio加载完成".getBytes());
    byteBuffer.flip();
    socketChannel.write(byteBuffer);
    //4.关闭socket通道和缓冲区
    byteBuffer.clear();
    socketChannel.close();
}

public void server() throws IOException {
    //1.获取服务端socket通道，并将其设置为非阻塞状态，绑定端口号
    ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
    serverSocketChannel.configureBlocking(false);
    serverSocketChannel.bind(new InetSocketAddress(9898));
    //2.创建selector选择器
    Selector selector = Selector.open();
    //3.将服务端socket通道注册到selector选择器中，并指定“监听接受事件”
    serverSocketChannel.register(selector, SelectionKey.OP_ACCEPT);
    //4.轮训式的获取selector上已经”准备就绪“的事件
    while (selector.select() > 0){
        //5.获取当前选择器中所有注册的”选择键（已就绪的监听事件）“
        Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
        while (iterator.hasNext()){
            //6.获取“准备就绪”的事件
            SelectionKey selectionKey = iterator.next();
            //7.判断具体是什么事件”准备就绪“
            if(selectionKey.isAcceptable()){
                //8.是”接受就绪“事件，那么获取客户端连接，设置为非阻塞式
                SocketChannel socketChannel = serverSocketChannel.accept();
                socketChannel.configureBlocking(false);
                //9.将该通道注册到选择器上
                socketChannel.register(selector,SelectionKey.OP_READ);
            }else if (selectionKey.isReadable()){
                //10.获取当前选择器上“读就绪”状态的通道
                SocketChannel socketChannel = serverSocketChannel.accept();
                //11.读通道中的内容
                ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
                int len = 0;
                while ((len = socketChannel.read(byteBuffer)) != -1){
                    byteBuffer.flip();
                    System.out.println(new String(byteBuffer.array(),0,len));
                    byteBuffer.clear();
                }
            }
        }
        //12.删除选择器上的迭代器
        iterator.remove();

    }
}
```

### DatagramChannel 

和socketChannel的使用类似，有些许不同。

代码：

```java
public void client() throws IOException {
    DatagramChannel datagramChannel = DatagramChannel.open();
    datagramChannel.configureBlocking(false);
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    byteBuffer.put("客户端udp发送成功".getBytes());
    byteBuffer.flip();
    //datagram通道直接将缓冲区发送给对应地址
    datagramChannel.send(byteBuffer,new InetSocketAddress("127.0.0.1",9898));
    byteBuffer.clear();
    datagramChannel.close();
}


public void server() throws IOException {
    DatagramChannel datagramChannel = DatagramChannel.open();
    datagramChannel.bind(new InetSocketAddress(9898));
    datagramChannel.configureBlocking(false);
    Selector selector = Selector.open();
    datagramChannel.register(selector, SelectionKey.OP_READ);
    while (selector.select() > 0){
        Iterator<SelectionKey> iterator = selector.selectedKeys().iterator();
        while (iterator.hasNext()){
            SelectionKey selectionKey = iterator.next();
            if(selectionKey.isReadable()){
                ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
                //datagram通道收到的内容写入到缓冲区中
                datagramChannel.receive(byteBuffer);
                byteBuffer.flip();
                System.out.println(new String(byteBuffer.array(),0,byteBuffer.limit()));
            }
        }
        iterator.remove();
    }
    datagramChannel.close();
}
```

# 管道

![image-20201118224238643](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201118224238643.png)

代码：

```java
public void pipeTest() throws IOException {
    Pipe pipe = Pipe.open();
    ByteBuffer byteBuffer = ByteBuffer.allocate(1024);
    Pipe.SinkChannel sinkChannel = pipe.sink();
    byteBuffer.put("管道通信".getBytes());
    byteBuffer.flip();
    sinkChannel.write(byteBuffer);

    Pipe.SourceChannel sourceChannel = pipe.source();
    byteBuffer.flip();
    sourceChannel.read(byteBuffer);
    System.out.println(new String(byteBuffer.array(),0,byteBuffer.limit()));
}
```

