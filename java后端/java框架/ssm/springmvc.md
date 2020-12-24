# spring mvc 

- 概述
  
- spring MVC是spring结构中的web模块，所有模块的运行都依赖于核心模块IOC
  
- 结构
  - MVC 结构图：MVC本身的思想
    
    - ![image-20200910121453112](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20200910121453112.png)
  - spring mvc 结构图：spring框架中MVC的结构

    ![](C:\Users\xpty\Desktop\java后端\java框架\springmvc.jpg)

# helloworld

- 导包

  - 引入tomcat7
    
    - <img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20200910131033829.png" alt="image-20200910131033829" style="zoom:10%;" />
  - 新建动态web工程
    - <img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20200910130958943.png" alt="image-20200910130958943" style="zoom:10%;" />
    - web 工程结构
      - ![image-20200910131119159](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20200910131119159.png)
  - 导jar包
    - 核心模块包
      - commons-logging-1.1.jar
        spring-beans-4.3.9.RELEASE.jar
        spring-context-4.3.9.RELEASE.jar
        spring-core-4.3.9.RELEASE.jar
        spring-expression-4.3.9.RELEASE.jar

    - web模块包
      - spring-web-4.3.9.RELEASE.jar
        spring-webmvc-4.3.9.RELEASE.jar
  
- 写配置：在web.xml中配置

  - web.xml ：配置前端控制器，和拦截器

    ```xml
    <!--  前端控制器 -->
    <servlet>
        <servlet-name>DispatcherServlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <!-- springmvc的配置文件的地址,他配置在src目录下 -->
            <param-value>classpath:springmvc.xml</param-value>
        </init-param>
        
        <!-- servlet 启动加载，servlet原本是第一次访问创建对象；
    	load-on-startup是服务器启动的时候创建对象，值越小越优先创建 -->
        <load-on-startup>1</load-on-startup>
    </servlet>
    
    <servlet-mapping>
        <servlet-name>DispatcherServlet</servlet-name>
        <!-- 拦截所有请求，"/*"也拦截所有请求一样，但它还会拦截 “*.jsp”这些请求，jsp页面被拦截就不能显示了 -->
        <url-pattern>/</url-pattern>
    </servlet-mapping>
    ```

  - spring.xml：就是spring bean configuration file ，扫描组件和配置视图解析器

    ```xml
    <!--扫描所有组件 -->
    <context:component-scan base-package="com.hsh.springmvc"></context:component-scan>
    <!--  配置视图解析器 -->
    <bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
        <property name="prefix" value="/WEB-INF/pages/"></property>
        <property name="suffix" value=".jsp"></property>
    </bean> 
    ```

- 测试

  - 文件夹结构图：WEB-INF下的index.jsp ，pages下的 success.jsp ， 还有 controller包下的HelloWorld 三个文件

    - <img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20200910165044708.png" alt="image-20200910165044708" style="zoom: 80%;" />

  - index.jsp

    - ```xml
      <html>
      <head>
      <meta charset="UTF-8">
      <title>Insert title here</title>
      </head>
      <body>
      	<a href="hello">helloword</a>		<!--一个通向域名为hello的超链接 -->
      </body>
      </html>
      ```

  - HelloWorld

    - ```java
      //@Controller 告诉springmvc 这是一个处理器组件，可以处理请求
      @Controller
      public class HelloWorld {
          //代表从当前项目下开始，处理当前项目下的hello请求
      	@RequestMapping("/hello")
      	public String firstRequest() {
      		System.out.println("请求收到，正在处理");
      		//return 返回一个页面地址，告诉服务器接下来加载这个页面，这一步还可以优化
              return "WEB-INF/pages.success.jsp";
              //配置了视图解析器后只需要 return success即可，视图解析器会自动拼接地址
              
      	}
      
      }
      ```

  - success

    - ```xml
      </head>
      <body>
      	<span>成功</span>
      </body>
      </html>
      ```

- helloworld 细节

  - 运行过程
    
    - 客户端点击链接，tomcat相应---> 前端控制器根据域名解析，在controller中根据requestMapping查找对应方法---->方法中进行业务处理，然后返回一个页面地址给前端控制器 ----> 前端控制器收到后，将这个页面发送给渲染模版进行渲染后发送给前端
    
  - 前端拦截器中 “/" , "/*" 的区别

    - "/**" 会拦截 .jsp 结尾的页面，而.jsp页面是tomcat来处理的，使用  /* * 后的结果

      - tomcat服务器的web.xml 相当于是 我们自己定义的web.xml的父类，在tomcat服务器的web.xml中有一个DefaultServlet 是 url-pattern=/，如果我们自己定义的web.xml中设置为url-pattern=/*，那么就覆盖了父类的设置，就会过滤掉请求中.jsp的页面。

      - 而DefaultServlet 是tomcat处理静态资源的，除.jsp，servlet外，其余都是静态资源，所以如果编写一个.html文件，而我们的web.xml中设置为 /*，那么这个请求就会被拦截

        

        

# @RequestMapping

- 概述

  - 告诉 springmvc 它可以处理什么url请求
  - @RequestMapping("/hello")  斜杠 可以省略
  - 他可以标注在**类**和**方法定义处**
    - 类上：提供初步请求映射信息，提供基准路径；相对于WEB应用的根目录
    - 方法上：进一步细分请求映射信息；相对于类定义处的URL，若类没有标注@RequestMapping，则相对于WEB应用的根目录

- 属性

  - **value** ：如@RequestMapping("/hello")，value可以省略，表示可以响应什么请求

  - **method**：限定请求方式，如 GET, POST

    - ```java
      @RequestMapping( method=RequestMethod.POST)   //表示只接受POST请求
      ```

  - **params**：规定请求参数

    - param1 : 

      - ```java
        @RequestMapping( params={"username"})   //请求必须带上一个名为username的参数，否则404
        @RequestMapping( params={"username=123"}) //请求必须带上一个名为username值为123的参数，否则404
        ```

    - !param1

      - ```java
        @RequestMapping( params={"!username=123", "pwd"})  
        //请求不能带上一个名为username值为123的参数，这个参数可以为null,也可以为空字符串，还要带上名为pwd的参数，它们两是与的关系，否则404
        ```

  - **headers**：规定请求头，也和params一样能写简单的表达式

    - 比如限制某些浏览器可以访问，某些不可以，这要用到请求头中的user-agent

    - ```java
      @RequestMapping( headers={"User-Agent=..."})  //在各自浏览器中获取 
      ```

  - **consumers**：只接受内容类型是哪种的请求，比如，规定请求头中的Content-Type

  - **produces**：告诉浏览器返回的内容类型是什么，比如，给响应头中加上Content-Type：text/html ; charset=utf-8

    

## Ant 的模糊匹配

- URL地址可以写模糊的通配符

  - ？：能替代任意一个字符

    - ```java
      @RequestMapping(value="antTest?")		//能响应antTest1,antTest2等等
      ```

  - *：能替代任意多个字符，和一层路径

    - ```java
      @RequestMapping(value="antTest*")		//能响应antTest001, antTestabc
      @RequestMapping(value="a/*/antTest")	//能响应a/abc/antTest，a/123/antTest 
      ```

  - **：能替代多层路径

    - ```java
    @RequestMapping(value="a/**/antTest")	//能响应a/abc/antTest，a/abc/123/antTest 
      ```
  
  - 注意

    - 若一个类中模糊和精确请求都存在，那么优先用精确的方法

  

## @PathVariable()

- 用来获取URL请求中的参数

  - ```java
    // URL=.../user/001 或 url=.../user/hhh
    //使用{id} 使得不同id的URL都能由这个方法来处理，而pathVariable注解指定一个变量来接受url中的id
    @RequestMapping(value="/user/{id}")
    public String pathVariableTest(@PathVariable("id")String id){
        System.out.println("收到参数"+id);
    }
    ```
```
    
  - @RequestMapping 中的value如果要用占位符，那么要在方法的参数前定义 @PathVariable()，括号内的值必须是占位符中的内容

## REST 

- 概述：针对URL的一种架构，希望以简洁的URL发送请求

  - 对于一个Book实体，总共会有增删改查四种操作，以前是写getBook，updateBook，addBook, deletBook 四个方法来实现。而在web开发中，从前端发来的URL请求如何识别用户需要进行哪种操作？
  - 在REST的思想下，对于同一个URL ” .../book“  有四种发送方式，后端根据前端的提交方式就可以识别如何进行响应
    - **GET获取资源**
    - **POST新建资源**
    - **PUT更新资源** 
    - **DELETE 删除资源**

- 问题：页面上只能发送 GET 和 POST两种方式，那么如何实现REST思想？

  - 首先在web.xml中配置 HiddenHttpMethodFilter

    - ```xml
      <filter>
          <filter-name>HiddenHttpMethodFilter</filter-name>
          <filter-class>org.springframework.web.filter.HiddenHttpMethodFilter</filter-class>
      </filter>
      <filter-mapping>
          <filter-name>HiddenHttpMethodFilter</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
```

  - 然后前端页面的**PUT DELETE请求都通过POST请求来转发，同时带上一个参数 _method**

    - ```jsp
      <form action="book" method="post">
          <input type="hidden" name="_method" value="put"/>
          <input type="submit" value="更新图书"/>
      </form>
      
      <form action="book" method="post">
          <input type="hidden" name="_method" value="delete"/>
          <input type="submit" value="删除图书"/>
      </form>
      ```

    - ```java
      @RequestMapping(value="/book")
      public String pathVariableTest(method=RequestMethod.PUT){
          System.out.println("更新图书");
      }
      
      @RequestMapping(value="/book")
      public String pathVariableTest(method=RequestMethod.DELETE){
          System.out.println("删除图书");
      }
      
      //后端就可以拦截到 put 和 delete 请求
      ```

    - 这种方法不适合于tomcat8及以上，对于tomcat8 及以上的解决方法

      - 需要在controller下返回的页面中添加isErrorPage="true"和 <% %>

        - ```xml
          <%@ page language="java" contentType="text/html; charset=UTF-8"
              pageEncoding="UTF-8" isErrorPage="true" %>
          ...
             <body>
              <% %>
              </body>
          ```

        - 

# 获取请求中的各种信息

获取请求的参数和对象的方法如下

- 默认方式获取参数

  - ```xml
    <a href="book?author=tom"></a>		//直接在？后面传参数的键值对，没有的话就传null
    ```

  - ```java
    @RequestMapping("/book")
    public String defaultMethod(String author){		//控制台方法的参数名称要和前台传来的参数名一致
         System.out.println("图书作者为"+author);
    }
    ```

- **@RequestParam**：从URL中获取请求参数，

  - ```xml
    url=.../book?id=1
    ```

  - ```java
    @RequestMapping("/book")
    public String defaultMethod(@RequestParam("id")String bid){	 //bid 保存从URL中根据注解获得到的参数值
         System.out.println("图书的编号为"+bid);
    }
    ```

  - 属性：@RequestParam(value="id"，required=“true”,defaultValue="1")

    - **value**：指定要获取的参数的key
    - **required**：这个参数是否必须，默认必须带
    - **defaultValue**：默认值

  - 和@PathVariable 的区别

    - @PathVariable 获取的是 URL中 ..../book/{id} 中的使用占位符的 ｛id}，而@RequestParam 获取的是URL中 ..../book?id=123 中的 id=123

- **@RequestHeader**：获取请求头中某个key的值

  - ```java
    ...(@RequestHeader("User-Agent")String userAgent, ...)  //获取请求头中的User-Agent，如果没有就报错
    ```

  - 属性

    - value：指定要获取的参数的key
    - required：这可参数是否必须，默认必须带
    - defaultValue：默认值

- **@CookieValue**：获取某个cookie的值

  - ```java
    ...(@CookieValue("JSESSION")String jid,...)    //获取请求中的JSESSION的值，如果没有就报错
    ```

  - 属性

    - value：指定要获取的参数的key
    - required：这可参数是否必须，默认必须带
    - defaultValue：默认值

- **页面传入的POJO对象springmvc自动封装**

  - index.jsp中

    - ```xml
      <form action="book">
          书名：<input type="bookName" name="bookName"/>
          作者：<input type="author" name="author"/>
          地址：省<input type="province" name="address.province" />
          市<input type="city" name="address.city" />
          <input type="submit"/>
      </form>
      ```

  - Book类

    - ```java
      public class Book{
          private String bookName;
          private String author;
          private Address address;
          ...
      }
      ```

  - Address类

    - ```java
      public class Address{
          private String province;
          private String city;
          ...
      }
      ```

  - controller 组件

    - ```java
      @RequestMapping("/book")
      public String pojoTest(Book book){
          System.out.println("收到图书"+book);
          return "success";
      }
      ```

- **springmvc 可以直接在参数上写原生API，如 HttpRequest 和 httpServlet等**

  - 原生API

    - HttpServletRequest
      - InputStream：请求流
      - reader：请求字符流
    - HttpServletResponse
      - OutputStream：响应流
      - writer：响应字符流
    - HttpSession 
    - Locale： 国际化有关的区域信息对象

  - controller 中

    - ```java
      @RequestMapping("/book")
      public String pojoTest(HttpServletRequest request,HttpServletResponse response,HttpSession session){
          request.setAttribute("reqParam", "request域");
          session.setAttribute("sesParam", "session域");
          ServletInputStream inputStream =request.getInputStream();
          ServletOutputStream outputStream =response.getOutputStream();
          BufferedReader br = request.getReader();
          PrintWriter bw = response.getWriter();
          return "success";
      }
      ```

  - success.xml 中就可以提取到web中request和session域中的属性

    - ```xml
      ...
      请求：${requestScope.reqParam}
      session:${requestScope.sesParam}			<!--显示 request域，session域-->
      ...
      ```

# 中文乱码问题

- 页面提交数据时可能出现乱码，分为 请求乱码 和 响应乱码 两种乱码问题

  - 请求乱码 

    - 对于GET 下只需要去tomcat下的server.xml中找到8080端口处，设置 URLEncoding="UTF-8"即可

    - 对于 POST 下需要在web.xml中配置过滤器

    - ```xml
      <!-- 配置一个字符编码的filter -->
      <filter>
          <filter-name>CharacterEncodingFilter</filter-name>
          <filter-class>org.springframework.web.filter.CharacterEncodingFilter</filter-class>
      	
          <init-param>	<!-- 指定编码为utf-8-->
              <param-name>encoding</param-name>
              <param-value>UTF-8</param-value>
          </init-param>
          <init-param>
              <!--顺手解决响应乱码： 源码中是response.setcharacterencoding(forceEncoding) -->
              <param-name>forceEncoding</param-name>
              <param-value>true</param-value>
          </init-param>
      </filter>
      
      <filter-mapping>
          <filter-name>CharacterEncodingFilter</filter-name>
          <url-pattern>/*</url-pattern>
      </filter-mapping>
      ```

  - 响应乱码

    - response.setContentType("text/html;charset=utf-8")

- 注意

  - 在web.xml中写完前端控制器之后就写这个字符编码的filter
  - tomcat安装完之后就在server.xml中的8080端口设置URLEncoding="UTF-8"
  - 字符编码的filter一定要写在其他filter之前，否则乱码还会出现

# 将数据输出给页面

**主要放在request域中**

- **原生API**

  -  HttpServletRequest
  - HttpServletResponse 
  - HttpSession 

- **在方法处传入**

  - Map

    - ```java
      @RequestMapping("/book")
      public String pojoTest(Map<String,Object> map){
      	map.put("msg","你好")
          return "success";
      }
      ```

  - Model：spring框架下的一个接口

    - ```java
      @RequestMapping("/book")
      public String pojoTest(Model model){
      	model.addAttribute("msg","hello");
          return "success";
      }
      ```

  - ModelMap

    - ```java
      @RequestMapping("/book")
      public String pojoTest(ModelMap modelMap){
      	modelMap.addAttribute("msg","hello");
          return "success";
      }
      ```

    - ```xml
      <!--在xml中以上三种方式都可以通过request域取出来 -->
      ${requestScope.msg}  
      ```

- **通过ModelAndView 传数据**

  - ```java
    @RequestMapping("/book")
    public String pojoTest(){
        //success.jsp页面去掉后缀就是视图名，视图解析器会通过拼串得到真实地址，最后返回ModelAndView 
    	ModelAndView mv = new ModelAndView ("success");
        mv.addObject("msg", "hello");
        return "mv";
    }
    ```

  - ```xml
    <!--在xml中还是可以通过request域取出来 -->
    ${requestScope.msg}  
    ```

- **通过Session 传数据**

  - 注解@SessionAttributes 的原理是保存数据的时候在session中也保存一份

  - ```java
    @SessionAttributes(value={"msg","haha"},types={String.class})
    public class example{
        ...
    }
    //value 指定给哪些参数名保存数据时就给session中复制一份
    //types 指定保存哪种类型的数据时，给session中复制一份
    //只能放在类上
    ```

  - 推荐使用原生API :HttpSession
  
- ModelAttribute：现在不用了

# 前端控制器DispatcherServlet源码

1. DispatcherServlet 类的继承树：**主要研究DispatcherServlet 类中的doDispatch( ) 方法**

   ![image-20201125172403055](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201125172403055.png)

2. doDispatch()方法源码：

   ```java
   protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
   		HttpServletRequest processedRequest = request;
   		HandlerExecutionChain mappedHandler = null;
   		boolean multipartRequestParsed = false;
   
   		WebAsyncManager asyncManager = WebAsyncUtils.getAsyncManager(request);
   
   		try {
   			ModelAndView mv = null;
   			Exception dispatchException = null;
   
   			try {
                   //1.检查是否为文件上传请求
   				processedRequest = checkMultipart(request);
   				multipartRequestParsed = (processedRequest != request);
   
   				// Determine handler for the current request.
                   //2.根据当前请求地址寻找哪一个handler(类)可以处理这个请求
   				mappedHandler = getHandler(processedRequest);
                   //3.如果没有找到哪个处理器（控制器）能处理这个请求，那就404或抛异常
   				if (mappedHandler == null) {
   					noHandlerFound(processedRequest, response);
   					return;
   				}
   
   				// Determine handler adapter for the current request.
                   //4.拿到能执行这个handler(类)的所有方法的适配器（反射工具，AnnotationMethodHandlerAdapter）
   				HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
   
   				// Process last-modified header, if supported by the handler.
   				String method = request.getMethod();
   				boolean isGet = "GET".equals(method);
   				if (isGet || "HEAD".equals(method)) {
   					long lastModified = ha.getLastModified(request, mappedHandler.getHandler());
   					if (new ServletWebRequest(request, response).checkNotModified(lastModified) && isGet) 						{
   						return;
   					}
   				}
   
   				if (!mappedHandler.applyPreHandle(processedRequest, response)) {
   					return;
   				}
                   try{
                       //5.适配器来执行目标方法。ha 就是刚刚获取到的适配器。将目标方法执行完成后的返回值作为视图名，设置保存						到modelAndview中。目标方法无论返回成什么，最终适配器都将执行完的信息包装成modelandview
   					mv = ha.handle(processedRequest, response, mappedHandler.getHandler()); 
                   }
   				finally{
                       if (asyncManager.isConcurrentHandlingStarted()) {
   						return;
   					}
                   }
                   //如果没有视图名那么就设置一个默认名
   				applyDefaultViewName(processedRequest, mv);
   				mappedHandler.applyPostHandle(processedRequest, response, mv);
   			}
   			catch (Exception ex) {
   				dispatchException = ex;
   			}
   			catch (Throwable err) {
   				// As of 4.3, we're processing Errors thrown from handler methods as well,
   				// making them available for @ExceptionHandler methods and other scenarios.
   				dispatchException = new NestedServletException("Handler dispatch failed", err);
   			}
               //转发到目标页面
               //6.根据方法执行完后封装的ModelAndView转发到目标页面，而且ModelAndView中的数据在请求域中也可以获取
   			processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
   		}
   		catch (Exception ex) {
   			triggerAfterCompletion(processedRequest, response, mappedHandler, ex);
   		}
       	catch (Throwable err) {
   			triggerAfterCompletion(processedRequest, response, mappedHandler,
   					new NestedServletException("Handler processing failed", err));
   		}
   		finally {
   			if (asyncManager.isConcurrentHandlingStarted()) {
   				// Instead of postHandle and afterCompletion
   				if (mappedHandler != null) {
   					mappedHandler.applyAfterConcurrentHandlingStarted(processedRequest, response);
   				}
   			}
   			else {
   				// Clean up any resources used by a multipart request.
   				if (multipartRequestParsed) {
   					cleanupMultipart(processedRequest);
   				}
   			}
   		}
   	}
   ```

3. mappedHandler = **getHandler**(processedRequest) 细节

   1. 作用：根据当前请求在HandlerMapping中找到这个请求的映射信息，获取目标处理器类。

   2. getHandler() 方法源码

      ```java
      protected HandlerExecutionChain getHandler(HttpServletRequest request) throws Exception {
      		if (this.handlerMappings != null) {
      			for (HandlerMapping mapping : this.handlerMappings) {
      				HandlerExecutionChain handler = mapping.getHandler(request);
      				if (handler != null) {
      					return handler;
      				}
      			}
      		}
      		return null;
      	}
      ```

      - HandlerMapping：处理器映射，保存了每个处理器能够处理哪些方法的映射信息。
      - handlerMappings：处理器映射的集合，保存了使用配置和使用注解的所有handler。
        - handlerMap：每一个HandlerMapping都保存一个handlerMap对象。IOC容器在启动创建Controller对象时扫描每个处理器都能处理什么请求，将这些信息保存在HandlerMapping的handlerMap属性中

4. HandlerAdapter ha = **getHandlerAdapter**(mappedHandler.getHandler()) 细节

   1. 作用：根据获得的目标处理器类，找到它对应的HandlerAdapter适配器

   2. getHandlerAdapter(mappedHandler.getHandler()) 源码：

      ```java
      protected HandlerAdapter getHandlerAdapter(Object handler) throws ServletException {
      		if (this.handlerAdapters != null) {
      			for (HandlerAdapter adapter : this.handlerAdapters) {
      				if (adapter.supports(handler)) {
      					return adapter;
      				}
      			}
      		}
      		throw new ServletException("No adapter for handler [" + handler +
      				"]: The DispatcherServlet configuration needs to include a HandlerAdapter that supports this handler");
      	}
      
      ```

      - handlerAdapters中有三种类型的适配器，我们需要的是AnnotationMethodHandlerAdapter，它是能解析注解方法的适配器，处理器类中只要有标了注解的方法就可以用。

5. 执行方法的调用

   ```java
   mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
   ```

6. invokeHandlerMethod()

7. invokeHandlerMethod()

   ```java
   1.拿到方法的解析器
   2.方法解析器根据当前请求地址找到真正的目标方法
   3.创建一个方法执行器
   4.包装原生的request,response
   5.创建一个隐含模型: 创建了一个BindingAwareModelMap，
   6.真正执行目标方法： 方法执行器.invokeHandlerMethod()。目标方法利用反射执行期间确定参数值，提前执行modelattribute等所有操作都在这个方法中
   ```

## 获取目标方法的参数

1. 确定方法每个参数的值
   1. 标注解：保存注解的信息，最终得到这个注解应该对应解析的值
   2. 没标注解
      1. 看是否是原生api
      2. 看是否是model或map，或等等
      3. 如果都不是，看是否是简单类型，
         1. 是的话给paramName赋值
         2. 不是的话给attrName赋值
            1.  参数标了@ModelAttribute 就是指定值
            2. 没标，就是空串
            3. 确定自定义类型的参数
               1. attrname使用参数的类型首字母小写，或者使用之前@ModelAttribute 指定值
               2. 先看隐含模型中有没有这个attrName作为key对应的值；如果有就从隐含模型中获取并赋值
               3. 看是否是@SessionAttributes(value="...") 标注的属性。
                  1. 是，从session中拿，拿不到就抛异常
               4. 不是，利用反射创建一个对象。
         3. 拿到之前创建好的对象，使用数据绑定器（WebDataBinder）将请求中的每个数据绑定到这个对象中。

## ModelAndAttribute

被@ModelAndAttribute标注的方法会提前运行，并把方法的运行结果标放在在隐含模型中。放的时候会使用一个key，如果注解指定了key，就用指定的值。反之，使用返回值类型的首字母小写作为key。

## @SessionAttribute

最好不要使用。如果要用要保证两点：1.隐含模型中有SessionAttribute标注的属性。2.如果隐含模型中有，session还说有就一定要有，否则抛异常。

## springmvc的九大组件

1. DispatcherServlet 类中的9个引用类型属性

   ```java
   /** 文件上传解析器*/
   	@Nullable
   	private MultipartResolver multipartResolver;
   
   	/** 区域信息解析器；与国际化有关 */
   	@Nullable
   	private LocaleResolver localeResolver;
   
   	/** 主题解析器 */
   	@Nullable
   	private ThemeResolver themeResolver;
   
   	/** Handler映射信息*/
   	@Nullable
   	private List<HandlerMapping> handlerMappings;
   
   	/** Handler的适配器 */
   	@Nullable
   	private List<HandlerAdapter> handlerAdapters;
   
   	/** springMVC的异常解析器 */
   	@Nullable
   	private List<HandlerExceptionResolver> handlerExceptionResolvers;
   
   	/**如果处理器返回值为空，那么就让请求 */
   	@Nullable
   	private RequestToViewNameTranslator viewNameTranslator;
   
   	/** springMVC中运行重定向携带数据的功能 */
   	@Nullable
   	private FlashMapManager flashMapManager;
   
   	/**视图解析器 */
   	@Nullable
   	private List<ViewResolver> viewResolvers;
   ```

2. 九大组件的初始化 ：DispatcherServlet 类中的initStrategies 方法调用九个方法来初始化

   组件的初始化时，有的组件在容器中使用类型找，有些组件使用ID找，如果没找到就使用默认配置

   ```java
   protected void initStrategies(ApplicationContext context) {
   		initMultipartResolver(context);
   		initLocaleResolver(context);
   		initThemeResolver(context);
   		initHandlerMappings(context);
   		initHandlerAdapters(context);
   		initHandlerExceptionResolvers(context);
   		initRequestToViewNameTranslator(context);
   		initViewResolvers(context);
   		initFlashMapManager(context);
   	}
   ```

   - initHandlerMappings 方法

     ```java
     private void initHandlerMappings(ApplicationContext context) {
     		this.handlerMappings = null;
     		//先在IOC容器中找所有的HandlerMappings
     		if (this.detectAllHandlerMappings) {
     			// Find all HandlerMappings in the ApplicationContext, including ancestor contexts.
     			Map<String, HandlerMapping> matchingBeans =
     					BeanFactoryUtils.beansOfTypeIncludingAncestors(context, HandlerMapping.class, true, false);
     			if (!matchingBeans.isEmpty()) {
     				this.handlerMappings = new ArrayList<>(matchingBeans.values());
     				// We keep HandlerMappings in sorted order.
     				AnnotationAwareOrderComparator.sort(this.handlerMappings);
     			}
     		}
         	//如果没找到，那么就从IOC容器中根据beanid来找对应的HandlerMapping
     		else {
     			try {
     				HandlerMapping hm = context.getBean(HANDLER_MAPPING_BEAN_NAME, HandlerMapping.class);
     				this.handlerMappings = Collections.singletonList(hm);
     			}
     			catch (NoSuchBeanDefinitionException ex) {
     				// Ignore, we'll add a default HandlerMapping later.
     			}
     		}
     
     		// Ensure we have at least one HandlerMapping, by registering
     		// a default HandlerMapping if no other mappings are found.
         	//还是没找到的话就使用默认的
     		if (this.handlerMappings == null) {
     			this.handlerMappings = getDefaultStrategies(context, HandlerMapping.class);
     			if (logger.isTraceEnabled()) {
     				logger.trace("No HandlerMappings declared for servlet '" + getServletName() +
     						"': using default strategies from DispatcherServlet.properties");
     			}
     		}
     	}

     ```
     
     









# 视图解析器ViewResolver

- forward 前缀

  - 对于已经配置了视图解析器后想要返回到同目录下jsp页面的解决方法

  - ```java
    @RequestMapping("/book")
    public String test1(){
        return "../../success";			// ”../“ 表示跳出当前目录
    }
    ```

  - ```java
    @RequestMapping("/book")
    public String test2(){
        return "forward:/success.jsp";		//有forward 表示不使用视图解析器拼串，不使用 / 则是相对路径
    }
    ```

- redirect 重定向

  - ```java
    @RequestMapping("/book")
    public String test3(){
        return "redirect:/success.jsp";			// 从当前项目下开始，springmvc自动为路径拼接上项目名
    }
    ```

## 视图解析过程

1. springmvc试图解析的特点

   1. 方法执行后的返回值会作为页面地址参考，转发或者重定向到页面
   2. 视图解析器可能会进行页面地址的拼串。
   3. 任何目标方法的返回值最后都会包装成ModelAndView

2. 视图解析过程

   1. 适配器执行目标方法

   2. 视图渲染方法

      ```java
      //跳转到目标页面
      processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
      ```

      视图渲染过程：将域中的数据在页面展示。页面就是用来渲染数据的。

   3. 渲染页面

      ```java
      render(mv, request, response);
      ```

   4. View视图对象与ViewResolver视图解析器

      ViewResolver的作用就是根据视图名（方法的返回值）得到View对象。

      1. 具体流程：所有配置的视图解析器都来尝试根据视图名（返回值）得到View（视图）对象。如果能得到就返回，得不到就换下一个视图解析器。然后View调用render方法
      2. 
      3. ViewResolver只是为了的到View，而View才能真正的转发或者重定向到页面，顺便将模型数据全部放到请求域中。View才能真正渲染视图。

   5. 视图解析器（ViewResolver接口）的实现类

      1. InternalResourceViewResolver：解析为URL文件
      2. BeanNameViewResolver：将视图名解析成一个bean

   6. 视图（View接口）的实现类

      1. InternalResourceView：将jsp封装成一个视图
      2. JstlView：如果jsp页面使用了jstl标签，则需要使用该视图类
      3. AbstractExcelView：将视图构造成Excel文档

## springMVC实现简单国际化

1. 创建不同的properties文件

2. 在web.xml中导入jstl包

   ```xml
   <bean class="org.springframework.web.servlet.view.InternalResourceView">
   	<property name="prefix" value="/WEB-INF/pages/"></property>
       <property name="suffix" value=".jsp"></property>
   </bean>
   ```

3. 使用jstlView，在web.xml中配置

   ```xml
   <!--让springmvc管理国际化资源文件只需要配置一个资源文件管理器，id必须叫messageSource -->
   <bean id="messageSource" class="org.springframework.context.support.ResourceBundleMessageSource">
       <!--basename指定基础名 -->
       <property name="basename" value="i18n"></property>
   </bean>
   ```

4. 直接去页面使用 < fmt:message >

   ```jsp
   <h1><fmt:message key="welcomeinfo"/></h1>
   <form action="">
   	<fmt:message key="username">:<input/><br/>
       <fmt:message key="password">:<input/><br/>
       <input type="submit" value='<fmt:message key="loginBtn"/>'/><br/>
   <form>
     
   ```

5. 浏览器根据使用的语言自动切换.

6. 注意：

   - 请求一定要过springMVC，不能直接访问jsp页面
   - 不能使用 ”forward:“ 来跳转页面

## view-controller

访问一个域名时控制台需要有一个方法来处理这个域名请求，如果只是想单纯的访问一个页面，那么这个控制台方法只需要跳转到某个页面，那么在业务开发中可能有许多这种只返回一个页面而不进行处理的方法。为了解决这个问题引入 view-controller 标签，目的是访问这个域名时直接跳转到WEB-INF下的页面即可，不用在控制台写方法。

1. 控制台的toLogin() 方法

   ```java
   @RequestMapping("/toLoginPage")
   public String toLogin(){
   	return "login"; //跳转到WEB-INF下的login页面
   }
   ```

2. web.xml中配置view-controller

   ```xml
   <!--path：指定域名 view-name：指定要跳转到的页面，它会被视图解析器拼串 -->
   <mvc:view-controller path="/toLoginPage" view-name="login"/>
   ```

3. web.xml 中还要开启mvc注解模式（高配版mvc），不然其他请求不能执行

   ```xml
   <mvc:annotation-driven></mvc:annotation-driven>
   ```


## annotation-driven

1. **两个都没配置**：动态资源（@RequestMapping映射的）能访问，静态资源（.html，.js，.img）不能访问。

   原因：DefaultAnnotationHandlerMapping中的handlerMap中保存了每一个动态资源的映射信息，没有保存静态资源映射的请求。

2. **只配置< mvc: default-servlet-handler/>**：静态资源能访问，动态资源不能访问。

   动态资源不能访问的原因：HandlerMapping中没有DefaultAnnotationHandlerMapping了，用SimpleUrlHandlerMapping替换了，他的作用是将所有请求发送给tomcat。

   静态资源能访问的原因：SimpleUrlHandlerMapping将所有请求都映射给tomcat了。

3. 两个都配置：< mvc: default-servlet-handler/> 和 < mvc: annotation-driven/>，动态静态都能访问

   原因：HandlerMapping中多了一个RequestMappingHandlerMapping，DefaultAnnotationHandlerMapping不见了。RequestMappingHandlerMapping中有一个HandlerMethod，它的属性保存了每一个请求用哪个方法来处理。HandlerAdapter中RequestMappingHandlerAdapter替换了原来的AnnotationMethodHandlerAdapter

# 数据转换&数据格式化&数据校验

1. 将request对象和携带的参数传递给WebDataBinderFactory实例，以创建DataBinder实例对象。
2. DataBinder调用ConversionService组件进行数据类型转换，数据格式化工作。将参数值填充到对象中。
3. 调用Validator组件对已经填充参数值的对象进行数据合法性校验，并最终生成数据绑定结果BindingData。
4. springmvc抽取BindingResult中的已经填充参数值的对象和校验错误对象，将它们赋给处理方法的响应入参。

![image-20201127105450147](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201127105450147.png)



## 数据绑定

前端利用js校验数据时如果浏览器进制js执行，那么校验功能就失败了。所以后端一定要自己校验。

日期格式化：

1. 使用@DateTimeFormat(pattern="yyyy-MM-dd")

   ```java
   @DateTimeFormat(pattern="yyyy-MM-dd") //规定了页面提交的日期格式
   private Date birth;
   ```

2. 如果系统中使用了自定义的数据类型转换，那么在web.xml 中要将注册的自定义类型转换器所属bean类型的class属性值用FormattingConversionServiceFactoryBean 替换原来的 ConversionServiceFactoryBean 类型。ConversionServiceFactoryBean接口没有格式化功能

数字格式化：

1. 使用@NumberFormat(pattern="#,###.####")：

   ```java
   @NumberFormat(pattern="#,###.####") //小数点后有多少#，表示保留几位小数
   private Double salary;
   ```

## 数据校验

Java有JSR303规范来做数据校验，只需要使用注解即可。市面上有第三方的校验框架。



# springMVC--Ajax

1. 导包：jackson的三个包

   Jackson-annotation.xxx.jar

   Jackson-core.xxx.jar

   Jackson-databind.xxx.jar

2. 使用注解

   - **@ResponseBody** 标注在方法上，那么方法返回的是响应体，返回的自定义对象会自动转换为json对象格式。

   - **@JsonIgnore** 标注在对象的变量上，那么返回给前端时会忽略对象的这个属性值

   - **@JsonFormat(pattern="yyyy-MM-dd")** 标注在Date变量上，自动将变量返回成规定好的格式

   - **@RequestBody**标注在方法参数上 ：表单的请求方法必须是 post，否则收不到请求体。

     ```java
     @RequestMapping("/xxx")
     public void method(@RequestBody String requestBody){
         //结果全是表单内容的字符串格式的键值对
         System.out.println(requestBody);  
     }
     ```

     @ResponseBody：可以把对象转为json对象。@RequestBody：可以接受json，封装成对象。

     ```java
     @RequestMapping("/xxx")
     public void method(@RequestBody Person person){
         ////前端以json格式发送过来时，@RequestBody将其封装成Person对象
         System.out.println(person);  
     }
     ```

   - **@HttpEntity< String>** 标注在方法参数上：接收请求头的所有信息。

     比@RequestHeade("xxx") 只能获取一个请求头中信息要强大

     ```java
     @RequestMapping("/xxx")
     public void method(@HttpEntity< String> header){
         //打印出请求头中所有信息
         System.out.println(header);  
     }
     ```

     ResponseEntity<byte[]>：自定义响应。

# 文件上传

1. 导包： fileupload和io包

2. 表单使用POST方法，enctype设置为multipart/form-data类型

3. web.xml中配置

   ```xml
   <!--id固定不能随便改 -->
   <bean id="multipartResolver" class="org.springframework.web.multipart.MultipartResolver">
       <!--设置文件上传最大尺寸 和 默认的编码格式-->
       <property name="maxUploadSize" value="#{1024*1024}"></property>
       <property name="defaultEncoding" value="utf-8"></property>
   </bean>
   ```

4. 在方法的参数上使用MultipartFile 类型的变量获取即可

   ```java
   //单文件上传
   @RequestMapping("/xxx")
   public void method(@RequestParam("xxx")MultipartFile file){
       //将文件保存到本地
   	String name = file.getOriginalFilename();
       file.transferTo(new File("H://"+name)); 
   }
   ============
   //多文件上传
   @RequestMapping("/xxx")
   public void method(@RequestParam("xxx")MultipartFile[] files){
       //将文件保存到本地
   	for(MultipartFile file : files){
           if(!file.isEmpty()){//文件项可能为空
               String name = file.getOriginalFilename();
       file.transferTo(new File("H://"+name)); 
           }
       }
       
       
   }
   ```

   

# 拦截器

springMVC的拦截器机制（HandlerInterceptor接口）允许运行目标方法之前进行一些拦截工作，或者目标方法运行之后进行一些处理，实现该接口即可使用拦截器。Filter是Javaweb的机制。

HandlerInterceptor接口 有三个方法：

- boolean preHandler() ：目标方法执行之前调用，返回true就放行
- void postHandler()：目标方法运行之后调用
- void afterCompletion()：在请求整个完成之后执行

执行顺序： preHandler --- 目标方法 --- postHandler --- 页面 -- afterCompletion

preHandler  不放行后面都不能执行；只要放行了，afterCompletion 一定执行

实现：

1. 写拦截器接口的实现类

2. web.xml中配置实现类，默认拦截所有请求

   ```xml
   <mvc:interceptor>
   	<bean class="实现类的全类名"></bean>
       <mvc:mapping path="要拦截的请求"></mvc:mapping>
   </mvc:interceptor>
   ```

多拦截器：

1. 多拦截器执行顺序由web.xml中配置顺序决定

2. 多拦截器正常执行下： 先执行的后返回，原理和多个过滤器执行时一样。

   1preHandler --- 2preHandler ---  method -- 2postHandler --1postHandler --- 2afterCompletion---1afterCompletion

3. 多拦截器执行出现异常：

   - 那一个拦截器不放行，那么后面的都不执行。
   - 2HandlerInterceptor 不放行时，1HandlerInterceptor 的afterCompletion还会执行。也就是说已经放行了的拦截器的afterCompletion一定会执行。

4. preHandler 按顺序执行，剩下两个按逆序执行。

5. 多拦截器执行流程图

   ![image-20201127224036854](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201127224036854.png)

拦截器和过滤器的区别：

1. 过滤器是Javaweb的三大组件之一，过滤器对象的创建交给tomcat完成，而不是IOC容器完成。所以，如果需要涉及其他组件使用过滤器很不方便。
2. 拦截器依赖于spring框架，如果不能使用spring框架时，拦截器就失效了。 

# 异常处理

springMVC主要有三类异常：

- ExceptionHandlerExceptionResolver：处理使用@ExceptionHandler标注的异常
- ResponseStatusExceptionResolver：处理使用@ResponseStatus标注的异常
- DefaultHandlerExceptionResolver：判断是否是springMVC自带的异常，前两个不能处理异常时有用
- SimpleMappingExceptionResolver：自定义异常解析器。通过配置的方式处理异常。优先级最低。

异常解析器尝试用这三类异常去解析异常，一个不行就换下一个。

1. @ExceptionHandler 的使用：标注在处理异常的方法上

   ```java
   //在方法上标注注解，参数为这个方法要处理的异常。可以将异常信息放在ModelAndView中
   @ExceptionHandler(value={ArithmeticException.class,NullPointerException.class})
   public ModelAndView handlerException(Exception e){
       ModelAndView modelAndView = new ModelAndView("myError");
       modelAndView.addObject("exception",e);
       return modelAndView;
   }
   ```

2. 将异常统一处理，使用 @ControllerAdvice 标注在类上

   ```java
   @ControllerAdvice
   public class MyExceptionHandler{
       
       @ExceptionHandler(value={ArithmeticException.class,NullPointerException.class})
       public ModelAndView handlerException(Exception e){
           ModelAndView modelAndView = new ModelAndView("myError");
           modelAndView.addObject("exception",e);
           return modelAndView;
       }
       ...
   }
   ```

   多个异常处理方法同时有用时，优先使用本类的异常处理方法，优先使用处理更具体异常的方法。

3. @ResponseStatus 的使用：标注在继承某个异常类的子类上

   ```java
   //专门处理这个状态码HttpStatus.NOT_ACCEPTABLE
   @ResponseStatus(reason = "用户被禁止登陆",value = HttpStatus.NOT_ACCEPTABLE)
   public class MyException extends RuntimeException{
   
   }
   ```

4. 手动定义异常解析器

   ```xml
   
   ```

   



# 国际化

原理就是改变springMVC的Locale信息。以下是点击链接切换语言的例子。

## 使用自定义的区域信息解析器

1. 创建一个实现了LocaleResolver接口的实现类

   ```JAVA
   public class MyResolver implements LocaleResolver {
       @Override
       public Locale resolveLocale(HttpServletRequest request) {
           Locale l = null;
           //前端在请求中设置一个locale变量
           String locale = request.getParameter("locale");
           if(locale!=null && !"".equals(locale)){
               l = new Locale(locale.split("_")[0], locale.split("_")[1]);
           }else {
               l = request.getLocale();
           }
           return l;
       }
   
       @Override
       public void setLocale(HttpServletRequest httpServletRequest, HttpServletResponse httpServletResponse, Locale locale) {
   
       }
   }
   ```

   

2. 在web.xml中配置自定义的区域信息解析器

   ```xml
   <bean id="localeResolver" class="com.hsh.bean.MyResolver"></bean>
   
   ```

   

## 使用sessionLocale

springMVC中还有其他的区域类型解析器，但只有sessionlocale是可以改变的，它的原理是从session中获取locale信息。

## 使用LocaleChangeInteceptor

这个拦截器会根据请求中的locale信息自动设置。

```xml
<mvc:interceptors>
    <bean class="org.springframework.web.servlet.i18n.LocaleChangeInterceptor"></bean>
</mvc:interceptors>

```



# 总结

1. springMVC 在配置文件中的设置
   1. 前端控制器
   2. 视图解析器
   3. 字符编码
   
2. **springMVC执行流程**

   1. 前端控制器（DispatchServlet）收到请求，调用doDispatch进行处理
   2. 根据HandlerMapping中保存的请求映射信息找到，处理当前请求，处理器执行执行链（包含拦截器）
   3. 根据当前处理器找到他的HandlerAdapter适配器
   4. 拦截器的preHandler先执行
   5. 适配器执行目标方法，并返回ModelAndView
      1. ModelAttribute注解标注的方法提前运行
      2. 执行目标方法的时候（确定目标方法的参数）
         1. 有注解
         2. 没注解
            1. 看是否是Model，map还是其他的
            2. 如果是自定义类型
               1. 从隐含模型中找，有就拿出来并跳出循环
               2. 如果没有，再看是否是SessionAttribute标注的属性，如果从seesion中拿不到就抛异常
               3. 都不是，就利用反射创建对象
   6. 拦截器的postHandler执行
   7. 处理结果（页面渲染流程）
      1. 如果有异常使用异常解析器处理异常；处理完后返回ModelAndView
      2. 调用render进行页面渲染
         1. 视图解析器根据视图名得到视图对象
         2. 视图对象调用render方法
      3. 执行拦截器的afterCompletion

   ![image-20201128124051489](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201128124051489.png)

