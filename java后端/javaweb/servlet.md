# Servlet 

***完整实例在eclipse的 TomcatTest文件下***

## servlet简介

1. servlet 是 JavaEE 规范之一，规范就是接口

2. servlet 是 JavaWeb三大组件之一。其余两个组件是Filter过滤器，Listener监听器

3. Servlet 是运行在服务器上的一个Java小程序。

   介于浏览器和web服务器之间的中间层，servlet可以获取浏览器的数据将其交给服务器，也可以将服务器端的数据库中信息交给浏览器。

Servlet 可以使用 **javax.servlet** 和 **javax.servlet.http** 包创建

![Servlet 架构](https://www.runoob.com/wp-content/uploads/2014/07/servlet-arch.jpg)



## serlvet 的创建和配置 tomcat

### tomcat 服务器文件的含义

<img src="https://www.runoob.com/wp-content/uploads/2016/01/232104410586765.png" alt="img" style="zoom: 80%;" />

- bin：二进制执行文件。里面最常用的文件是**startup.bat**，如果是 Linux 或 Mac 系统启动文件为 **startup.sh**。
- conf:配置目录。里面最核心的文件是**server.xml**。可以在里面改端口号等。默认端口号是8080，也就是说，此端口号不能被其他应用程序占用。
- lib：库文件。tomcat运行时需要的jar包所在的目录
- logs：日志
- temp：临时产生的文件，即缓存
- webapps：web的应用程序。**web应用放置到此目录下浏览器可以直接访问**
- work：编译以后的class文件。

双击 startup.bat 启动 Tomcat，会弹出一个窗口显示server startup 这表示本地服务器启动成功，在浏览器中输入 **http://localhost:8080/** 如果能够跳转到tomcat 的页面，表示tomcat安装成功并启动成功

### eclipse中 servlet 环境配置

1. 下载tomcat

2. 在eclipse中的 preference --> server ---> runtime enviorment 中添加tomcat服务器
3. new 一个 dynamic web project ---> 添加 jdk 和 tomcat ，记得生成 web.xml 文件 

工程文件结构图

![img](https://www.runoob.com/wp-content/uploads/2016/01/232113367466511.png)

- **deployment descriptor**：部署的描述。
- **Web App Libraries**：自己加的包可以放在里面。
- **build**：放入编译之后的文件。
- **WebContent**: 放进写入的页面。

在WebContent目录下新建一个jsp文件，输入 <%out.print("hello world"); %>  启动项目即可在浏览器输出正确的结果

## servlet 的生命周期

执行Servlet的构造器方法-----> 执行init()方法 ---> 执行service 方法 ----> 执行destory 方法

- Servlet 通过调用 **init ()** 方法进行初始化。

  - 只调用一次。它在第一次创建 Servlet 时被调用，在后续每次用户请求时不再调用。

  - ```java
    public void init() throws ServletException {
      // 初始化代码...
    }
    ```

- Servlet 调用 **service()** 方法来处理客户端的请求。

  - 执行实际任务的主要方法，浏览器的每一次的请求都会调用service方法。Servlet 容器（即 Web 服务器）调用 service() 方法来处理来自客户端（浏览器）的请求，一个请求生成一个线程，并把格式化的响应写回给客户端。service() 方法检查 HTTP 请求类型（GET、POST、PUT、DELETE 等），并在适当的时候调用 doGet、doPost、doPut，doDelete 等方法。

  - doGet() ：处理一个未指定method的http请求

    - ```java
      public void doGet(HttpServletRequest request,
                        HttpServletResponse response)
          throws ServletException, IOException {
          // Servlet 代码
      }
      ```

  - doPost()：处理一个制定了method为post的http请求

    - ```java
      public void doPost(HttpServletRequest request,
                         HttpServletResponse response)
          throws ServletException, IOException {
          // Servlet 代码
      }
      ```

- Servlet 通过调用 **destroy()** 方法终止（结束）。

  - 在 Servlet 生命周期结束时被调用，只会被调用一次。destroy() 方法可以让您的 Servlet 关闭数据库连接、停止后台线程、把 Cookie 列表或点击计数器写入到磁盘，并执行其他类似的清理活动。在调用 destroy() 方法之后，servlet 对象被标记为垃圾回收。

  - ```java
    public void destroy() {
        // 终止化代码...
      }
    ```

- 最后，Servlet 是由 JVM 的垃圾回收器进行垃圾回收的。

<img src="https://www.runoob.com/wp-content/uploads/2014/07/Servlet-LifeCycle.jpg" alt="Servlet 生命周期" style="zoom:80%;" />

## servlet 实例的创建

两种创建方式：继承HttpServlet抽象类，声明Servlet接口两种，通常用前者。

### 继承HttpServlet抽象类

详情见eclipse中的TomcatTest文件。idea中继承这个类需要自己重写方法

Web 应用程序开发人员通常编写 Servlet 来扩展 javax.servlet.http.HttpServlet，并实现 Servlet 接口的抽象类专门用来处理 HTTP 请求。

在dynamic web project 下，servlet 也可以像其他java类一样创建，步骤如下：

1. 在src包下面新建一个包com.hsh.test 包，在这个包中新建一个Serlvet类，这个类名字叫HelloServlet ，它继承了HttpServlet抽象类，

   ```java
   public class HelloServlet extends HttpServlet{
       //局部变量
       //init()方法
        @Override
       public void init(ServletConfig config) throws ServletException {
           super.init(config);	//这句话不能丢，否则不能获得到该Servlet对象的ServletConfig对象
           //因为在父类GenericServlet中的Init方法里保存了ServletConfig对象，如果子类重写了init方法，且没有调用父类的init方法，那么在当前的HelloServlet中不能获取到自己对应的ServletConfig对象
       }
       //service中的方法
       //destory()方法
   }
   ```

2. 在HelloServlet类中的doGet方法中填写如下代码

   ```java
   protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
       // TODO Auto-generated method stub
       // 使用 GBK 设置中文正常显示
       response.setCharacterEncoding("GBK");
       response.getWriter().write("hello world");
   }
   ```

3. 在web.xml 文件中配置，将刚才创建的servlet对象添加到服务器中，代码如下

   ```xml
   <web-app version="2.5"   
       xmlns="http://java.sun.com/xml/ns/javaee"   
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"   
       xsi:schemaLocation="http://java.sun.com/xml/ns/javaee   
       http://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">  
     <servlet>  
        <!-- 类名 -->  
       <servlet-name>HelloServlet</servlet-name>  
       <!-- 所在的包 -->  
       <servlet-class>com.runoob.test.HelloServlet</servlet-class>  
     </servlet>  
       
     <servlet-mapping>  
       <servlet-name>HelloServlet</servlet-name>  
       <!-- 访问的网址 -->  
       <url-pattern>/TomcatTest/HelloServlet</url-pattern>  
       </servlet-mapping>  
       
   </web-app>  
   ```

   使用 servlet标签和servlet-mapping标签

4. 启动项目即可在浏览器中看到hello world

### 声明Servlet接口

详情见idea中的ServletTest文件，这个方法不常用。

1. 在web项目中新建一个HelloServlet类，该类声明Servlet接口

   ```java
   public class HelloServlet implements Servlet {
   
       @Override
       public void init(ServletConfig servletConfig) throws ServletException {
   
       }
   
       @Override
       public ServletConfig getServletConfig() {
           return null;
       }
   
       @Override
       public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
           System.out.println("helloServlet accessing");
       }
   
       @Override
       public String getServletInfo() {
           return null;
       }
   
       @Override
       public void destroy() {
   
       }
   }
   ```

2. 在web.xml 中配置HelloServlet

   作用是 **将域名和Java源代码对应起来**	

   ```xml
   <servlet>
       <servlet-name>HelloServlet</servlet-name>
       <servlet-class>com.hsh.test.HelloServlet</servlet-class>
   </servlet>
   <servlet-mapping>
       <servlet-name>HelloServlet</servlet-name>
       <url-pattern>/hello</url-pattern>
           <!--/ : 服务器在解析时地址为 http://ip:端口号/工程路径 -->
       	<!--/hello : 服务器在解析时地址为 http://localhost:8080/ServletTest/hello  -->
       	<!--当浏览器访问该域名时，服务器会将请求发送给HelloServlet类 -->
   </servlet-mapping>
   ```

3. 启动tomcat即可在控制台看到打印信息

### web.xml中常见配置错误

1. url-pattern  标签中 域名没有以 / 打头
2. servlet-class 标签中 全类名输错，推荐直接复制全类名
3. 两个 servlet-name 标签中内容不相同

### 注意

1. 在idea中可以直接新建一个Servlet对象，它会自动生成doGET和doPost方法

## servlet 涉及的类和接口

### Servlet类的继承体系

**Servlet 接口----->  GenericServlet 类 ------> HttpServlet 类 -----> HttpServlet的实现类**

1. Servlet 接口 定义了Servlet的行为规范
2. GenericServlet 类声明了 Servlet接口，做了很多空实现，并持有一个ServletConfig类的引用
3. HttpServlet 类实现了Service方法，对浏览器的GET和POST请求做了分发处理
4. HttpServlet的实现类 根据业务需求重写doGet和doPost方法即可

### ServletConfig类

在使用 声明Servlet接口 这个方法创建Servlet实例时会出现ServletConfig类，这个类定义了一些可以返回**关于Servlet信息的**方法。但是在除init方法外也可以调用 **getServletConfig()** 来获取该Servlet的ServletConfig类对象。

一个Servlet对象有对应的一个ServletConfig对象。

#### 作用

1. 可以获取 Servlet程序的别名，即web.xml中  Servlet-name 标签中的值。
2. 获取初始化参数 init-param。
3. 获取 ServletContext 对象。

ServletConfig使用实例：

```java
public class HelloServlet implements Servlet {

    @Override
    public void init(ServletConfig servletConfig) throws ServletException {
        String initParameter = servletConfig.getInitParameter("abc");	//1234
        String servletName = servletConfig.getServletName();	//HelloServlet
        ServletContext servletContext = servletConfig.getServletContext();
        //org.apache.catalina.core.ApplicationContextFacade@515de15
    }
    @Override
    public void service(ServletRequest servletRequest, ServletResponse servletResponse) throws ServletException, IOException {
        ServletConfig servletConfig = getServletConfig();	
    }
    ...
}
```

web.xml中内容

```xml
<servlet>
    <servlet-name>HelloServlet</servlet-name>
    <servlet-class>com.hsh.test.HelloServlet</servlet-class>
    <init-param>				<!--初始化参数必须定义在servlet标签内，且只有当前servlet可以使用该参数 -->
        <param-name>abc</param-name>
        <param-value>1234</param-value>
    </init-param>
</servlet>
<servlet-mapping>
    <servlet-name>HelloServlet</servlet-name>
    <url-pattern>/hello</url-pattern>
</servlet-mapping>
```



### ServletContext 接口

1. 一个接口，表示Servlet的上下文对象。
2. 一个Web工程对应一个ServletContext对象。
3. ServletContext是一个域对象，这个域就是整个web工程。
4. ServletContext对象在web工程启动时创建，关闭时销毁。

域对象：像map一样存取数据的对象，这里的域指的是存取数据的操作范围。域对象存取数据一般为setAttribute()，getAttribute()。

#### 作用

1. 获取web.xml中配置的上下文参数 context-param
2. 获取当前工程路径，格式： /工程路径
3. 获取工程部署到服务器硬盘上的绝对路径
4. 向map一样存取数据

ServletContext 使用实例

```java
@Override
protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    ServletContext context = getServletContext();
    String a = context.getInitParameter("a");
    String b = context.getInitParameter("b");
    String contextPath = context.getContextPath();
    String realPath = context.getRealPath("/resources");
    System.out.println("初始化参数："+a+","+b);
    System.out.println("当前工程路径："+contextPath); //输出的是当前module中web目录的地址
    System.out.println("工程部署路径："+realPath);		//输出的是web目录下resources被部署在服务器端磁盘上的位置
    /**
    初始化参数：apple,banana
	当前工程路径：/servletTest_war_exploded
	工程部署路径：H:\ideaworkpath\workspace01\out\artifacts\servletTest_war_exploded\resources
    */
    context.setAttribute("key1","value1");
    Object key1 = context.getAttribute("key1");
    System.out.println(key1);		//输出value1
}
```

```xml
<!--ServletContext 对象只能获取到context-param标签下的参数，不能获取某个Servlet标签下的init-param标签的内容 -->
<context-param>
    <param-name>a</param-name>
    <param-value>apple</param-value>
</context-param>
<context-param>
    <param-name>b</param-name>
    <param-value>banana</param-value>
</context-param>
```

idea 工程结构图

![image-20201026110656243](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201026110656243.png)





### HttpServletRequest类

每次有http请求发送给tomcat，tomcat就会把http协议信息解析好封装在Request对象中，然后传递到Service方法中供我们使用，我们用HttpServletRequest可以获取到所有的请求的信息。

#### 常用方法

1. getRequestURI()：获取请求资源路径
2. getRequestURL()：获取请求的统一资源路径（绝对路径）
3. getRemoteHost()：获取客户端IP地址
4. getHeader(paraName)：获取请求头中某个参数的值
5. getParameter(paraName)：获取参数的值
6. getParameterValue()：获取多个参数的值
7. getMethod()：获取请求的方式（GET/POST)
8. setAttribute(key,value)：设置域数据。 这个域数据的生命周期取决于当前的request
9. getAttribute(key)：返回域数据
10. getRequestDispatcher()：获取请求转发对象
11. req.setCharacterEncoding("UTF-8") : 解决中文乱码问题，要放在获取参数之前

### HttpServletResponse类

每次有http请求发送给tomcat，tomcat都会创建一个response对象传递给Servlet使用，然后传递到Service方法中供我们使用，我们用HttpServletRequest可以获取到所有的请求的信息。

1. getWriter() 和 getOutputStream()  两种输出字符流只能同时使用一个。

2. response.setCharacterEncoding("UTF-8")  设置服务器的字符集为UTF-8

   response.setHeader("Content-Type","text/html;charset=UTF-8")  通过响应头设置浏览器也使用UTF-8字符集

   response.setContentType("text/html;charset=UTF-8") 同时设置服务器，浏览器，响应头都使用UTF-8

## servlet 表单数据

浏览器使用两种方法可将信息传递到 Web 服务器，分别为 GET 方法和 POST 方法。

前端的HTML页面中只有form表单用POST方式发送请求时使用的是http协议的POST方式，其余都为GET方式

**GET方法**

- 浏览器使用的默认方法，该方法会生成一个很长的字符串来包含浏览器中的信息，所以不适用于传输密码等重要信息。而且，GET方法有大小限制，最多传输1024个字符
- 服务端用doGet() 方法来处理 GET请求

**POST方法**

- 浏览器向后台传输数据的可靠方法，它会将信息包装成一个单独的消息，以标准的形式发送到服务端。服务端用doPost() 方法来处理POST请求

**使用servlet 读取表单数据**

Servlet 处理表单数据，这些数据会根据不同的情况使用不同的方法自动解析：

- **getParameter()：** 调用 request.getParameter() 方法来获取表单参数的值。
- **getParameterValues()：**如果**参数出现一次以上**，则调用该方法，并返回多个值，例如复选框。
- **getParameterNames()：**如果想要得到当前请求中的**所有参数的完整列表**，则调用该方法。

### GET实例

- **使用URL 的GET 方法实例**

  1. 创建名为 HelloForm 的servlet对象，实现从请求中获取参数并打印到浏览器中，然后再web.xml 中配置这个类对象

     ```java
     @WebServlet("/HelloForm")
     public class HelloForm extends HttpServlet {
        ... //省略构造方法
         protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             // 设置响应内容类型
             response.setContentType("text/html;charset=UTF-8");
     
             PrintWriter out = response.getWriter();
             String title = "使用 GET 方法读取表单数据";
             // 处理中文
             String name =new String(request.getParameter("name").getBytes("ISO-8859-1"),"UTF-8");
             String docType = "<!DOCTYPE html> \n";
             out.println(docType +
                 "<html>\n" +
                 "<head><title>" + title + "</title></head>\n" +
                 "<body bgcolor=\"#f0f0f0\">\n" +
                 "<h1 align=\"center\">" + title + "</h1>\n" +
                 "<ul>\n" +
                 "  <li><b>站点名</b>："
                 + name + "\n" +
                 "  <li><b>网址</b>："
                 + request.getParameter("url") + "\n" +
                 "</ul>\n" +
                 "</body></html>");
         }
         
         // 处理 POST 方法请求的方法
         public void doPost(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
             doGet(request, response);
         }
     }
     ```

  2. 在浏览器中输入URL：http://localhost:8080/TomcatTest/HelloForm?name=嘻嘻哈哈&url=www.xxhh.com

  3. 浏览器获取到name和 url 并显示

     ![image-20201012145251859](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201012145251859.png)

- **使用表单的 GET 方法实例**

  ​	在刚才的基础上

  1. 新建一个hello.html 文件在WebContent文件夹下

     ```html
     <form action="HelloForm" method="GET">
     	网址名：<input type="text" name="name">
     <br />
     	网址：<input type="text" name="url" />
     	<input type="submit" value="提交" />
     </form>
     ```

  2. 然后访问 ：http://localhost:8080/TomcatTest/Hello.html 填写表单并提交，显示如下

     ![image-20201012145305016](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201012145305016.png)

### POST 实例

- 使用表单的POST方法实例

  - 只需要将hello.html中表单的提交方法改为POST即可，不用在HelloServlet中的doPost方法会调用doGet方法处理请求。

- 将复选框数据传递到 Servlet程序

  1. 创建一个checkBox.html文件在WebContent下

     ```html
     <form action="CheckBox" method="POST" target="_blank">
         <input type="checkbox" name="java"/>Java
         <input type="checkbox" name="c"/>C
         <input type="checkbox" name="php"/>PHP
         <input type="submit" value="提交"/>
     </form>
     ```

  2. 创建一个名为CheckBox的Servlet类，并在web.xml中配置

     ```java
     ...
     out.println(docType+
                 "<html>\n" +
                 "<head><title>" + title + "</title></head>\n" +
                 "<body bgcolor=\"#f0f0f0\">\n" +
                 "<h1 align=\"center\">" + title + "</h1>\n" +
                 "<ul>\n" +
                 "  <li><b>java标识：</b>: "
                 + request.getParameter("java") + "\n" +		//根据复选框的name属性来查找
                 "  <li><b>c 标识：</b>: "
                 + request.getParameter("c") + "\n" +
                 "  <li><b>PHP标识：</b>: "
                 + request.getParameter("php") + "\n" +
                 "</ul>\n" +
                 "</body></html>"
     );
     ```

  3. 运行起来，就可以获取到表单的信息

- 读取所有表单的参数：太繁琐了



## servlet 客户端HTTP请求

浏览器请求网页时会向web服务器发送一些信息，这些信息作为http的请求头的一部分进行传输的。可以在服务端利用 ***HttpServletRequest*  对象**获取到这些信息。



### 使用GET方式的HTTP请求

由 **请求行 和 请求头** 两部分组成。

**请求行中的信息**

请求的方式  GET/ POST

请求的资源路径  

请求的协议的版本号

### 使用POST方式的HTTP请求

由请求行，请求头，请求体组成。

请求行和请求头和使用GET方式的请求一样，只有请求体有所不同。请求体存储发送给服务器的数据。



**常用的读取http请求头的方**法

| 序号 | 方法 & 描述                                                  |
| :--- | :----------------------------------------------------------- |
| 1    | **Enumeration getHeaderNames()** 返回一个枚举，包含在该请求中包含的所有的头名。 |
| 2    | **String getHeader(String name)** 以字符串形式返回参数指定的请求头的值。 |
| 1    | **Cookie[] getCookies()**  返回一个数组，包含客户端发送该请求的所有的 Cookie 对象 |

**常用的请求头中的信息**

| 头信息          | 描述                                                  |
| :-------------- | :---------------------------------------------------- |
| Accept          | 表示客户端可以接收的数据类型                          |
| Accept-Language | 表示客户端可以接收的语言类型                          |
| Cookie          | 这个头信息把之前发送到浏览器的 cookies 返回到服务器。 |
| Host            | 表示请求时的服务器和端口号                            |
| User-Agent      | 表示客户端浏览器的信息                                |

### 常见的状态码

1. 200：请求成功
2. 302：请求重定向
3. 404：请求服务器收到了，但是请求的内容不存在（地址有误）
4. 500：请求服务器收到了，但是服务器内部有错误（代码有误）

**获取客户端http请求的实例**

1. 创建一个名为DisplayerHeader的servlet类

   ```java
   //doGet方法中
   ...
   Enumeration<String> header = request.getHeaderNames();	//返回请求头中所有的信息
   while(header.hasMoreElements()) {
       String paramName = (String)header.nextElement();
       out.print("<tr><td>" + paramName + "</td>\n");
       String paramValue = request.getHeader(paramName);	//返回请求头中和参数名称相同的参数的值
       out.println("<td> " + paramValue + "</td></tr>\n");
   }
   ...
   ```

2. 在web.xml 中配置DisplayerHeader类，然后运行即可

## servlet 服务端响应一个HTTP请求

当一个 Web 服务器响应一个 HTTP 请求时，响应通常包括**响应行、响应头、响应体**。响应行包括 HTTP 版本（在本例中为 HTTP/1.1）、一个状态码（在本例中为 200）和一个对应于状态码的短消息（在本例中为 OK）。响应体用来存储发送给客户端的信息。

利用HttpServletResponse 对象实现。前面一直用的setContentType方法就是服务端响应请求的方法。

在浏览器每5秒刷新一次时间

1. 创建一个名为Refresh的Servlet类

   ```java
   //doGet 方法
   ...
   // 设置刷新自动加载时间为 5 秒
   response.setIntHeader("Refresh", 5);
   // 设置响应内容类型
   response.setContentType("text/html;charset=UTF-8");
   Calendar ca = Calendar.getInstance();
   Date time = ca.getTime();
   SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd HH:mm:ss");
   String nowTime = sdf.format(time);
   PrintWriter out = response.getWriter();
   String title = "自动刷新 Header 设置 - 菜鸟教程实例";
   String docType =
       "<!DOCTYPE html>\n";
   out.println(docType +
               "<html>\n" +
               "<head><title>" + title + "</title></head>\n"+
               "<body bgcolor=\"#f0f0f0\">\n" +
               "<h1 align=\"center\">" + title + "</h1>\n" +
               "<p>当前时间是：" + nowTime + "</p>\n");
   ...
   ```

2. 在web.xml中配置

## Servlet 请求转发

一个业务需要多个Servlet处理时，需要请求转发。比如，Servlet1对请求进行基本验证，Servlet2对请求进行完整的验证。

获得请求转发对象时，参数必须以 / 打头，表示映射到当前工程路径

```java
//Servlet1的doGet方法中
...	
    //先获取请求的转发对象，参数就是转发的目标，然后跳转到目标Servlet2
    RequestDispatcher requestDispatcher = req.getRequestDispatcher("/Servlet2");
	requestDispatcher.forward(req,resp);
...
```

注意：

1. 虽然浏览器访问到了Servlet2，但地址栏并没有变化，因为这被视作是一次请求而不是两次请求，同时Servlet1和Servlet2共享同一个Request域。
2. 利用请求转发可以访问到WEB-INF目录下的资源，但不能访问到当前工程外的资源（不能转发到百度网址）。

### base标签

定义当前HTML文档跳转时的基准，也就是说想要跳转到其他页面时需要在base标签定义的地址的基础上操作。

```html
<head>
    <meta charset="UTF-8">
    <title>Title</title>
    <base href="http://localhost:8080/servletTest/web/resources/a.html">
</head>
<body>
    <a href="../b.html">跳转到b.html</a>	
    <!--a标签会跳转到http://localhost:8080/servletTest/web/b.html-->
</body>
```

### / 在不同位置的含义

在web中斜杠是一种绝对路径

1. 在浏览器中 / 被解析为 http://ip:port/， 比如，a标签的href等于 / 
2. 在服务器中 /  被解析为  http://ip:port/工程路径
3. 服务器端的特殊情况：response.sendRedirect("/") ，它的作用是将 / 发送给浏览器去解析，浏览器解析得到 http://ip:port/

## Servlet请求重定向

不同于请求转发，请求重定向是原来的域名被废弃了，当浏览器仍旧访问旧的域名时，服务器会发送一个状态码为302，并包含新地址的响应，浏览器收到后会访问新的域名，所以这是两次请求，不同于请求转发。



![image-20201026231558517](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201026231558517.png)

```java
//第一种方法
//doGet方法中
...
	response.setStatus(302);
	response.setHeader("location","http://www.baidu.com"); //不能访问WEB-INF目录
...
-------------------
//第二种方法
    response.sendRedirect("http://www.baidu.com");

----------------------


```

**注意：重定向中 / 代表的是端口号，而不是工程路径**，使用时需要用 req.getContextPath() 方法获得工程路径。

## servlet 编写Filter过滤器

**过滤器是一个实现了 javax.servlet.Filter 接口的 Java 类。在拦截到请求后做出相应动作**

可以将一个或多个 Servlet 过滤器附加到一个 Servlet 或一组 Servlet。 servlet过滤器 和 servlet 之间是**多对多**的关系。

Servlet 过滤器也可以附加到 JavaServer Pages (JSP) 文件和 HTML 页面。 jsp 和 html页面也可以过滤。

- **在客户端的请求访问后端资源之前，拦截这些请求。**
- **在服务器的响应发送回客户端之前，处理这些响应。**

过滤器通过 Web 部署描述符（**web.xml**）中的 **XML 标签来声明**，当 Web 容器启动 Web 应用程序时，它会为您在部署描述符中声明的每一个过滤器创建一个实例。

过滤器使用场景：权限检查，事务管理，日记操作等等。

Filter的执行顺序与在web.xml配置文件中的配置顺序一致，一般**把Filter配置在所有的Servlet之前**。

### web.xml 中配置过滤器

过滤器使用之前需要在web.xml 中先配置才可以

```xml
<filter>
	<filter-name>LogFilter</filter-name>		<!--LogFilter是实现Filter接口的类，还有他的包名 -->
	<filter-class>com.hsh.test.LogFilter</filter-class>
	<init-param>
		<param-name>Site</param-name>			<!--设置了一个初始化时使用的参数 -->
		<param-value>嘻嘻哈哈</param-value>
	</init-param>
</filter>  
<filter-mapping>						<!--设置LogFilter这个过滤器都过滤那些Servlet，/* 表示过滤所有Servlet -->
	<filter-name>LogFilter</filter-name>
	<url-pattern>/*</url-pattern>
</filter-mapping>
```

过滤器没有用到的标签

- `<servlet-name>`指定过滤器所拦截的Servlet名称。
- `dispatcher>`指定过滤器所拦截的资源被 Servlet 容器调用的方式，可以是`REQUEST`,`INCLUDE`,`FORWARD`和`ERROR`之一，默认`REQUEST`。用户可以设置多个`<dispatcher>`子元素用来指定 Filter 对资源的多种调用方式进行拦截。
- `<dispatcher>`子元素可以设置的值及其意义
  - `REQUEST`：当用户直接访问页面时，Web容器将会调用过滤器。如果目标资源是通过RequestDispatcher的include()或forward()方法访问时，那么该过滤器就不会被调用。
  - `INCLUDE`：如果目标资源是通过RequestDispatcher的include()方法访问时，那么该过滤器将被调用。除此之外，该过滤器不会被调用。
  - `FORWARD`：如果目标资源是通过RequestDispatcher的forward()方法访问时，那么该过滤器将被调用，除此之外，该过滤器不会被调用。
  - `ERROR`：如果目标资源是通过声明式异常处理机制调用时，那么该过滤器将被调用。除此之外，过滤器不会被调用。

### Filter 接口

创建一个声明 Filter接口 的实现类就是创建了一个过滤器，在Filter接口中有3个方法

- **public void init(FilterConfig filterConfig)** ： 

  web 应用程序启动时，web 服务器将创建Filter 的实例对象，并调用其init方法，读取web.xml配置，完成对象的初始化功能，从而为后续的用户请求作好拦截的准备工作（filter对象只会创建一次，init方法也只会执行一次）。开发人员通过init方法的参数，可获得代表当前filter配置信息的FilterConfig对象。

- **public void doFilter (ServletRequest, ServletResponse, FilterChain)**
  该方法完成实际的过滤操作，当客户端请求方法与过滤器设置匹配的URL时，Servlet容器将先调用过滤器的doFilter方法。FilterChain用户访问后续过滤器。

- **public void destroy()**
  Servlet容器在销毁过滤器实例前调用该方法，在该方法中释放Servlet过滤器占用的资源。

### Filter生命周期

​	1.构造器方法

2. init()：1,2两个方法在启动web工程时就执行
3. doFilter()：每次拦截到请求时就执行
4. destory()：关闭web服务器时执行

### FilterConfig 类

Filter过滤器的配置文件类，tomcat每次创建Filter时创建一个FilterConfig，它包含Filter的配置信息。

作用：

1. 获取Filter在web中配置的 filter-name 的内容，还有init-param的值
2. 获取ServletContext 对象

### FilterChain 接口

FilterChain 就是过滤器链，控制多个过滤器的执行。

![](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201029203452183.png)

Filter的执行顺序由他们在web.xml中定义的顺序决定。

如果注释掉Filter2的chain.doFilter() 和 后置代码2 ，那么执行完Filter2的前置代码1，就会执行Filter1的后置代码2，而不会执行目标资源。

### Filter 拦截路径

1. 精确匹配：< url-pattern>/resource/a.jsp < /url-pattern>
2. 目录匹配：< url-pattern>/resource/* < /url-pattern>
3. 后缀名匹配：< url-pattern>*.jsp < /url-pattern> ，没有用斜杠打头，拦截所有以 .jsp结尾的请求

### 过滤器实例

1. 前面已经在 web.xml 中配置好了过滤器

2. 创建一个名为 LogFilter 的Filter接口实现类

   ```java
   public class LogFilter implements Filter {
       ...
       //
       public void init(FilterConfig fConfig) throws ServletException {
           String filterName =fConfig.getFilterName();			//LogFilter
   		String initParameter = fConfig.getInitParameter("Site");	//在init方法中获取filter中设置的参数
   		ServletContext servletContext = fConfig.getServletContext();	//ServletContext
   		System.out.println("网站名称: "+ initParameter);
   	}
       
       public void doFilter(ServletRequest request, ServletResponse response, FilterChain chain) throws IOException, ServletException {
   		System.out.println("站点网址：http://www.xxhh.com");
   		// pass the request along the filter chain
   		chain.doFilter(request, response);
   	}
       
       public void destroy() {
   		
   	}
}
   ```
   
   启动服务器时，访问任意一个域名，控制台首先打印 "网站名称:嘻嘻哈哈"，然后打印一次 "站点网址：http://www.xxhh.com" 。 继续访问当前页面就会再打印一次 ”站点网址...“ 。



## servlet 异常处置

异常处理的实例继承 HttpServlet

当一个 Servlet 抛出一个异常时，Web 容器在使用了 exception-type 元素的 **web.xml** 中搜索与抛出异常类型相匹配的配置。在 web.xml 中使用 **error-page** 元素来指定对特定**异常** 或 HTTP **状态码** 作出相应的 Servlet 调用。

### 异常处理实例

1. 创建一个名为ErrorHandler的异常处理的实例，将这个对象放进<Tomcat-installation-directory>/webapps/ROOT/WEB-INF/classes 中，但我没找到在哪

   ```java
   protected void doGet(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
       //获取请求中的异常信息
       Throwable throwable = (Throwable)request.getAttribute("javax.servlet.error.exception");
       Integer statusCode = (Integer)request.getAttribute("javax.servlet.error.status_code");
       String servletName = (String)request.getAttribute("javax.servlet.error.servlet_name");
       if (servletName == null){
           servletName = "Unknown";
       }
       String requestUri = (String)request.getAttribute("javax.servlet.error.request_uri");  
       if (requestUri == null){
           requestUri = "Unknown";
       }
   	//根据异常信息来显示相应指示
       ...
   }	
   ```

2. 在web.xml 中配置ErrorHandler 异常处理实例 和 http故障码，

   ```xml
     <servlet>
     	<servlet-name>ErroeHandler</servlet-name>		<!-- -->
     	<servlet-class>com.hsh.test.ErroeHandler</servlet-class>
     </servlet>
     <servlet-mapping>
     	<servlet-name>ErroeHandler</servlet-name>
     	<url-pattern>/TomcatTest/ErroeHandler</url-pattern>
     </servlet-mapping>
     
     <error-page>				<!--注册故障码 -->
     	<error-code>404</error-code>
     	<location>/TomcatTest/ErroeHandler</location>
     </error-page>
     <error-page>
     	<error-code>java.lang.Throwable</error-code>	<!--servlet异常也由ErrorHander来处理 -->
     	<location>/ErrorHandler</location>
     </error-page>
   ```

3. 启动服务器，搜索一个错误的url



## servlet cookie

cookie 是**存储在客户端计算机上的文本文件**，其中保留着用户各种信息。java servlet 支持处理 http cookie。

识别返回用户包括三个步骤：

- 服务器脚本向浏览器发送一组 Cookie。例如：姓名、年龄或识别号码等。
- 浏览器将这些信息存储在本地计算机上，以备将来使用。
- 当下一次浏览器向 Web 服务器发送任何请求时，浏览器会把这些 Cookie 信息发送到服务器，服务器将使用这些信息来识别用户。

### cookie 解析

Cookie 通常设置在 HTTP 头信息中，但JavaScript 也可以直接在浏览器上设置一个 Cookie

### 通过servlet 设置 cookie

主要经过3个步骤：

1. 创建cookie对象

   ```java
   Cookie cookie = new Cookie("key","value");
   -----------
   //获取提交表单中的url属性，并用它创建一个cookie对象
       Cookie url = new Cookie("url",request.getParameter("url"));
   ```

   

2. 设置cookie存活时间

   ```java
   cookie.setMaxAge(60*60*24);  //单位是秒
   -------
       url.setMaxAge(60*60*24);
   ```

   

3. 将cookie对象加入响应中

   ```java
   response.addCookie(cookie);
   -------
       response.addCookie(url);
   ```

   

### 通过servlet 获取 cookie

```java
Cookie[] cookies = request.getCookies();  //获得请求头中Cookie存储的所有名称值对象
Cookie cookie = cookies[0];     //将请求头中的第一个对象赋给cookie
cookie.getName();   //获得该cookie的名称
cookie.getValue();  //获得该cookie的值
```



### 通过servlet 删除 cookie

将cookie对象的最大生存时间改为0，然后将它加入到响应头中即可

```java
cookie.setMaxAge(0);  //该cookie对象被删除
response.addCookie(cookie);
```





## servlet session

http协议是一个 “无状态” 协议，这意味着**客户端每一次请求都是建立一条独立的连接到web服务器**，服务器并不保存之前客户端请求的任何记录。

以下四种方式用来维持 web服务器和客户端之间的 session会话

### Cookies

web服务端分配一个唯一的session ID 作为客户端的cookie，服务端在后续的请求中用这个id 来识别收到的cookie。但多数浏览器不支持，所以不推荐使用这种方式

### 隐藏的表单字段

### URL 重写

### httpSession 对象

Servlet 提供了 HttpSession接口，该接口提供了一种**跨多个页面请求或访问网站时识别用户以及存储有关用户信息**的方式



## servlet 数据库访问

## servlet 文件上传

Servlet 可以与 HTML form 标签一起使用，上传的文件可以是文本文件或图像文件或任何文档。

## servlet 处理日期

## servlet 重定向

最简单的方式是使用 response 对象的 sendRedirect() 方法

## servlet 计数器

### 网页点击计数器



### 网站点击计数器



# 补充

## servlet 的作用

介于浏览器 和 服务器 之间，用来收取和发送数据

## web.xml 的作用

介于服务器 和 代码 之间，像一个容器，让服务器知晓代码中都有那些实例，要怎么跟代码交流，

