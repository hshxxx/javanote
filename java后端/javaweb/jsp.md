# jsp

jsp全程 java servlet page，用来代替Servlet向浏览器传送数据，因为Servlet开发和维护成本高。

jsp文件在编译的时候会生成字节码文件，打开之后是一个继承了HttpServlet的类，所以**jsp本质上就是一个Servlet类**。

## page 命令

page命令可以修改JSP页面中重要的属性或行为

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>
```

page 命令的属性：

1. contentType：jsp返回的数据类型是什么

2. language：jsp页面翻译后是什么编程语言文件

3. pageEncoding：jsp页面当前本身使用的字符集

4. import：导包，导类

5. autoFlush：out输出流满了之后是否自动刷新缓冲区，默认为true

6. buffer：设置out缓冲区大小，默认为8KB

7. errorPage：当前jsp页面出错后跳转到errorPage指向的页面

   1. ```jsp
      <page errorPage="/error.html"></page>
      ```

   2. 以 / 打头，代表http:/ip:port/工程路径/

8. isErrorPage：设置当前jsp页面是否异常，默认是false，若为true，则会生成exception对象

9. session：设置当前jsp页面是否创建session对象，默认是true

10. extends：设置当前jsp页面继承哪个类，默认不修改。

## JSP脚本

### 声明脚本

用来声明对象

1. 格式：**<%!  java代码 %>**

   ```jsp
   <%!
   	//java代码
   %>
   ```

2. 实例

   ```jsp
   <%!
   	private int a;			//声明类属性
   
   	static{					//声明静态代码块
           private Map<String,String> map;
           map.put("a",1);
       }
   
   	private void method(){	//声明方法
           System.out.Println("1111");
       }
   
   	private class inner{	//声明内部类
           private String name;
       }
   %>
   ```

   最后在jsp页面生成的字节码文件中会添加这些资源

### 表达式脚本

用来在浏览器打印对象

1. 格式： **<%= java代码%>**

2. 实例

   ```jsp
   <%= 12 %>
   <%= abc %>
   <%= map %>
   <%= request.getParameter("username") %>   <!--还能获取请求中的参数 -->
   -----------
   <!-- 浏览器页面-->
   12
   abc
   {a,1}
   ```

3. 表达式脚本的特点

   - 所有的表达式脚本被翻译到 jsp的 _jspService方法中
   - 表达式脚本被out.print() 方法打印到页面上
   - 表达式脚本的内容可以使用 _jspService方法中的对象，比如request和 response
   - 表达式脚本不需要自己添加 ; 

### 代码脚本

1. 格式： **<% java代码%>**

2. 实例

   ```jsp
   <% 
   	for(int i=0; i<10;i++){
   		System.out.Println(i);    	//在控制台会打印
       }
   %>
   ```

   ```jsp
   <!-- 代码脚本和表达式脚本组合使用,实现在浏览器打印-->
   <% 
   	for(int i=0; i<10;i++){
   %>        
        <%= i%> <br>			
   <%		   	
       }
   %>
   ```

3. 代码脚本特点

   - 所有的表达式脚本被翻译到 jsp的 _jspService方法中
   - 表达式脚本的内容可以使用 _jspService方法中的对象，比如request和 response
   - 多个代码脚本和表达式脚本可以组合在一起使用

## 三种注释

### HTML注释

<!--HTML注释 -->

会被out.write() 传送到浏览器

### Java注释

// Java注释

会被放在 _jspService方法中

### jsp注释

<%--jsp注释  --%>

显示在jsp页面中

## JSP九大内置对象

1. request：请求对象
2. response：响应对象
3. pageContext：jsp上下文对象
4. session：会话对象
5. application：ServletContext 对象
6. config：ServletConfig 对象
7. out：jsp输出流对象
8. page：指向当前的jsp对象
9. exception：当前jsp页面的异常对象

### JSP四大域对象

| 域对象      | 所属类               | 作用范围                       |
| ----------- | -------------------- | ------------------------------ |
| pageContext | PageContextImpl类    | 当前jsp页面                    |
| request     | HttpServletRequest类 | 一次请求内                     |
| session     | HttpSession类        | 打开浏览器到关闭浏览器这段时间 |
| application | ServletContext类     | web工程的服务器运行期间        |

### jspout 和 response 的区别

```jsp
<%
	out.write("out输出</br>");
    response.getWriter().write("resonse输出</br>");
%>
------------
<!--浏览器中的输出，尽管out的输出在上面，但还是response先输出 -->
response输出
out输出

```

底层原理

![image-20201027112243789](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201027112243789.png)

由于jsp页面的字节码文件中都使用out来打印，所以一般情况下在jsp页面使用out，避免打乱页面输出内容。

### out.write 和 out.print的区别

out.write() 输出整型时出错

out.print() 输出任何类型都正确

原因：out.write(10) 在执行时将10转成char型，所以输出的是10对应的ascll码值，并不是10。

​			out.print(10) 在执行时将10转成String，所以输出的还是10

所以为了不出错，**一律使用out.print() 输出数据**

## JSP常用标签

### 静态包含

多个浏览器页面之间通常都有相同的组成，为了减少代码书写，可以在当前jsp页面直接引入别的jsp页面。

1. 格式：**<%@ include file="/index.jsp"%>**  

   include 属性的值是你要引入的jsp页面的路径

2. 原理： 

   静态包含其实是将被包含的jsp页面原封不动的拷贝到当前jsp页面。

   被包含的jsp页面并不会生成对应的字节码文件。

### 动态包含

1. 格式： **< jsp:include page="/index.jsp">< /jsp:include>**

   include 属性的值是你要引入的jsp页面的路径；效果和静态包含一样。

2. 原理

   动态包含是将当前jsp页面的request和response发送给被调用jsp页面，然后这两个页面都共用同一个out缓冲区。

### 标签转发

1. 格式：**< jsp:forward page="/index.jsp">< /jsp:forward>**

   page 属性的值是你要转向的jsp页面的路径；实现请求转发。

   

## 请求转发实例

![image-20201027132936506](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201027132936506.png)

## Listener监听器

Java web 三大组件：servlet程序 , Filter过滤器 , Listener监听器。

Listener是一种规范，也就是接口。

监听某种事物的变化，然后通过回调函数返回给程序去做相应的操作。

### ServletContextListener 监听器

监听ServletContext 的创建和销毁

```java
public class ServletListenerTest implements ServletContextListener {
	//分别在web工程的服务器启动和关闭时打印
    @Override
    public void contextInitialized(ServletContextEvent servletContextEvent) {
        System.out.println("ServletContext 被创建");
    }

    @Override
    public void contextDestroyed(ServletContextEvent servletContextEvent) {
 		System.out.println("ServletContext 被销毁");
    }
}
```

```xml
<listener>
    <listener-class>com.hsh.test.ServletListenerTest</listener-class>
</listener>
```

## EL 表达式

全称 Expression Language，用来**代替jsp的表达式脚本**。

```jsp
<body>
    <%
    request.setAttribute("key","value");
    %>
    <%=
    request.getAttribute("key")
    %><br/>
    ${key}
</body>
```

### 语法

1. 格式： **${表达式}**

   在表达式为Null时，表达式脚本会显示 null ，而EL表达式会显示空字符串。

   EL表达式对于复杂的bean对象是利用它的getter方法实现的，所以记得生成getter方法。

2. 实例

   ```jsp
   <!--显示变量，数组，list,map属性，省略了person的创建 -->
   <body>
   	<%
       	Person person = new Person();
       	person.setName("hsh");
       	person.setPhones(new String[]{"123","234"});
       	List<String> list = new ArrayList<>();
       	list.add("l1");
       	person.addCities(list);
   	    Map map = new Map<String,String>();
       	map.put("key1","value1");
       	person.setMap(map);
       	request.setAttribute("p",person);
       %>
       获取变量的值${p.name}
   	获取数组中第2个值${p.phones[1]}
       获取list集合中第1个值${p.cities[0]}
       获取map集合中键为key1的值${p.map.key1}
       
   </body>
   ```

3. 注意

四个域数据同时有相同名称的值的情况

```jsp
<body>
    <%
    	pageContext.setAttribute("key","pagecontext");
    	request.setAttribute("key","request");
    	session.setAttribute("key","session");
    	application.setAttribute("key","application");
    %>
    ${key}	<!--四个域数据都有相同名称的值，优先选择作用范围最小的，若没有，则按作用范围依次查找 -->
</body>
```

### 运算

#### 关系运算

< , > , <=, >=, ==, != 

#### 逻辑运算

&&, ||, ! 

#### 算术运算

1. +, - , * , / ，%

#### empty运算

${empty 变量} ：在判断下面的值时返回true

1. 值为null时
2. 值为空串时
3. 值是object类型数组，长度为0时
4. list集合元素个数为0时
5. map集合元素为0时

#### 三元运算

表达式1 ？ 表达式2： 表达式3

#### . 运算 和 [] 运算

前面已经展示过如何使用，但还有如下特殊情况：所要获取的值的名字中有 . 的情况

```html
<body>
    <%
		Map<String,String> map = new HashMap<>();
    	map.put("a.a.a","aaavalue");
    	request.setAttribute("map",map);
    %>
    ${map['a.a.a']}  	<!--用['']的形式就可以正确显示 -->
</body>
```



### EL 表达式隐含的11个对象

这11个对象是EL自己定义的，可以直接使用

| 变量             | 类型                 | 作用                             |
| ---------------- | -------------------- | -------------------------------- |
| pageContext      | PageContextImpl      | 获取jsp的9个内置对象             |
| pageScope        | Map<String,Object>   | 获取pageContext域中数据          |
| requestScope     | Map<String,Object>   | 获取request域中数据              |
| sessionScope     | Map<String,Object>   | 获取session域中数据              |
| applicationScope | Map<String,Object>   | 获取ServletContext域中数据       |
| param            | Map<String,String>   | 获取一个参数的值                 |
| paramValues      | Map<String,String[]> | 获取多个参数的值                 |
| header           | Map<String,String>   | 获取请求头的信息                 |
| headerValues     | Map<String,String[]> | 获取请求头中多个值的信息         |
| cookie           | Map<String,Cookie>   | 获取当前请求的cookie信息         |
| initParam        | Map<String,String>   | 获取web.xml中配置的context-param |

在多个域数据中有相同名称的值时，就可用pageScope， requestScope，sessionScope，applicationScope来精准表示需要显示的值。

**pageContext**的使用：

1. 一般用来显示网络相关信息

```jsp
<body>
    协议: ${pageContext.request.scheme} <br>
    服务器IP：${pageContext.request.serverName}<br>
    服务器端口：${pageContext.request.serverPort} <br>
    获取工程路径：${pageContext.request.contextPath} <br>
    获取请求方法：${pageContext.request.method} <br>
    获取客户端IP地址 ${pageContext.request.remoteHost} <br>
    获取会话ID编号：${pageContext.session.id} <br>
</body>
<!-- 
协议: http
服务器IP：localhost
服务器端口 8080
获取工程路径：/servletTest_war_exploded
获取请求方法：GET
获取客户端IP地址 127.0.0.1
获取会话ID编号：A8B1CF7FAFD8C137B2D5E9AC16C59606
-->
```

param 和paramValue 的使用:

```jsp
<body>
    <!--获取URL中的username的值和hobby的第一个值，因为hobby可以有多个 -->
	${param.username}		
    ${paramValues.hobby[0]}
</body>
```

header 和 headerValues的使用：

```jsp
<body>
    <!--获取请求头中的User-Agent的值，因为有特殊字符所以用中括号，这两个返回的值相同 -->
	${header['User-Agent']}		
    ${headerValues['User-Agent'][0]}
</body>
```

cookie的使用

```jsp
<body>
    <!--获取COOKIE中的sessionID的名字 -->
	 ${cookie.JSESSIONID.name}
</body>
```

init-param的使用

```jsp
<body>
    <!--获取web.xml中配置的context-param的参数 -->
	 ${initParam.url}
</body>
```

## JSTL

全称 JSP Standard Tag Library 叫做 jsp 标准标签库，用来**替换 jsp的代码脚本**，一共有5个库

![image-20201028120838804](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201028120838804.png)

使用JSTL标签库需要在 jsp 页面中引入

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201028120929132.png" alt="image-20201028120929132" style="zoom:80%;" />

### 使用步骤

1. 导入JSTL所需要的JAR包，将它添加到Library中后就可以使用了。

   ![image-20201028121426720](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201028121426720.png)

2. 直接使用JSTL的标签，idea自动导入JSTL标签库。

   

   

### CORE 标签库

1.  **c:set** 

   ```jsp
   <body>
   修改之前：${requestScope.aaa} </br>
   <c:set scope="request" var="aaa" value="123"/>
   修改之后：${requestScope.aaa}
   </body>
   ----------
   <%=
      request.setAttribute("aaa","123");  原来的方法
   %> 
   ```

   scope属性：选择你要设置的变量是哪个域的

   var属性：变量名； value属性：变量值

2.  **c:if**

   ```jsp
   <body>
       <c:if test="${12==12}">
       	<h1>12==12</h1>
   	</c:if>
   </body>
   ```

   test属性：判断表达式是否正确，此处的表达式用的是EL表达式。

   没有if/else 结构

3.  **c:choose，when, otherwise**

   ```jsp
   <body>
   <%
       request.setAttribute("height",180);
   %>
   <c:choose>
       <c:when test="${requestScope.height>=180}">
           <h1>大于180</h1>
       </c:when>
       <c:when test="${requestScope.height>=170}">
           <h1>大于170</h1>
       </c:when>
       <c:when test="${requestScope.height>=160}">
           <h1>大于160</h1>
       </c:when>
       <c:otherwise>
           <c:choose>
               <c:when test="${requestScope.height>=150}">
                   <h1>大于150</h1>
               </c:when>
           </c:choose>
       </c:otherwise>
   </c:choose>
   </body>
   ```

   test属性：判断表达式的值是否正确

   注意：choose标签中只能使用jsp的注释；

   ​			when标签的父标签必须是choose标签，所以在otherwise标签中想要继续判断就要继续使用choose标签

4. **c:forEach**

   - **遍历数字：begin="1" end="10" var="i" step="2"**

     ```jsp
     <body>
         <c:forEach begin="1" end="10" var="i" step="2">
         <tr>
             <td>
                 第${i}行
             </td>
         </tr>
     </c:forEach>
     </body>
     ```

     begin属性就是开始，end属性就是结束，var属性就是当前正在遍历到的变量，step属性就是步长

   - **遍历Object数组：items="${requestScope.telephone}" var="phone"**

     ```jsp
     <body>
     <%
         request.setAttribute("telephone",new String[]{"110","120","119"});
     %>
     
     <c:forEach items="${requestScope.telephone}" var="phone">
         ${phone}<br>
     </c:forEach>
     </body>
     ```

     原理很类似与java的增强for循环 ( for(Object o: list) ) ，items属性的值就是要遍历的集合或数组，var就是当前遍历的对象

   - **遍历map集合：items="${requestScope.map}" var="entry"**

     ```jsp
     <body>
     <%
         Map<String,String> map = new HashMap();
         map.put("key1","value1");
         map.put("key2","value2");
         map.put("key3","value3");
         request.setAttribute("map",map);
     %>
     
     <c:forEach items="${requestScope.map}" var="entry">
         map对象：${entry},map键：${entry.key},map值:${entry.value} <br>
     </c:forEach>
     </body>
     ```

   - 遍历对象：和遍历map集合类似

5. 属性

   | 属性      | 含义                                         |
   | --------- | -------------------------------------------- |
   | begin     | 开始标记                                     |
   | end       | 结束标记                                     |
   | var       | 遍历的下标                                   |
   | items     | 被遍历的集合                                 |
   | step      | 遍历的步长                                   |
   | varStatus | 存储当前遍历的信息，比如begin，end的设置等等 |

   