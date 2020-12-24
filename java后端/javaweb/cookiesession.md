# Cookie

服务器通知客户端保存键值对的一种技术。

cookie由Servlet程序生成然后发送给浏览器去保存，然后在浏览器每次发送请求时都将cookie发送给服务器。

cookie值可以唯一的标识客户端。它的大小固定为不大于4KB。

以下实例都在 idea的 cookiesession模块下

## 创建cookie

```java
protected void createCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie = new Cookie("key1", "value1");
    resp.addCookie(cookie);
    resp.getWriter().write("cookie添加成功");
}
```

## 服务器获取cookie

```java
protected void getCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie[] cookies = req.getCookies();
    for (Cookie cookie : cookies) {
        resp.getWriter().write("cookie["+cookie.getName()+"="+cookie.getValue()+"] <br/>");
    }

    Cookie iwantCookie = javautil.findCookie("key1",cookies);	//获取指定cookie的工具类
    if(iwantCookie != null){
        resp.getWriter().write("找到需要的cookie");
    }


}
```

```java
public class javautil {
    public static Cookie findCookie(String name, Cookie[] cookies){
        if(name==null || cookies==null || cookies.length==0){
            return null;
        }
        for (Cookie cookie : cookies) {
            if(name.equals(cookie.getName()))
                return cookie;
        }
        return null;
    }
}
```

## 修改cookie

原理就是覆盖原有的cookie，对于修改过或者新添加的cookie都要记得resp.addCookie才能生效 

```java
protected void updateCookie(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    //方法一
    Cookie cookie = new Cookie("key1", "newvalue1");
    resp.addCookie(cookie);
    //方法二
    Cookie[] cookies = req.getCookies();
    Cookie findCookie = javautil.findCookie("key1", cookies);
    if(findCookie!=null){
        findCookie.setValue("newvalue2");
        resp.addCookie(findCookie);
    }

}
```

## cookie生命周期

setMaxAge()：括号内为时间，单位为秒

​	正数，表示在指定时间后删除

​	负数，表示浏览器关闭时删除，和session生命周期相同

​	0，表示马上删除，不用等

## 有效路径path设置

path 是cookie的一个属性，这个属性通过**请求的地址**来**过滤**哪些cookie发送到服务器，那些不能发送到服务器。

例子：

​		cookie1  path=/工程路径

​		cookie2 path=/工程路径/abc

现在浏览器向 http://ip:port/工程路径 这个地址发送请求，cookie1 会发送给服务器，但cookie2不会。

若向  http://ip:port/工程路径/abc  这个地址发送请求，cookie1 和cookie2 都会发送给服务器。



设置cookie的path属性

```java
protected void testPath(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
    Cookie cookie = new Cookie("key2", "value2");
    cookie.setPath(req.getContextPath()+"/abc");
    resp.addCookie(cookie);
}
```

## 用户免输入登录实例

思路图

![image-20201029115028709](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201029115028709.png)

1. login.jsp 页面

   ```jsp
   <body>
       <form action="http://localhost:8080/cookiesession_war_exploded/login" method="get">
           用户名：<input type="text" name="username" value="${cookie.username.value}">
           密码：<input type="password" name="password">
           <input type="submit" value="登陆">
       </form>
   </body>
   ```

2. Login Servlet类

   ```java
   public class Login extends HttpServlet {
       @Override
       protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws ServletException, IOException {
           String username = "hsh";
           String password = "123";
           if (username.equals(req.getParameter("username")) && password.equals(req.getParameter("password"))){
               Cookie username1 = new Cookie("username", username);
               username1.setMaxAge(60*60*24*7);
               resp.addCookie(username1);
               System.out.println("用户名已添加到cookie中");
           }else{
               System.out.println("登录失败");
           }
       }
   }
   ```

3. web.xml中配置 Login类

当登录成功一次之后，第二次就会在用户名出自动添加

# Session

Session 就是一个接口 (HttpSession)。

session 就是会话，用来维持浏览器和服务器之间关联的技术，存储在服务器端。

每个客户端都有一个自己的Session会话。通常用来保存用户登录之后的信息。

## 创建和获取Session

涉及的方法：

```java
request.getSession() //第一次调用是创建session，第二次是获取
isNew() //当前session是不是刚创建的
getId() //获取当前的session的id
```



## 获取session中的数据

session是域数据

```java
getSession().setAttribute("key1","value1")  //往session中添加数据
getSession().getAttribute("key1")   //获取session中数据
```

## session超时控制

session超时指的是两次请求的最大间隔，超过时间就会被销毁。tomcat默认是30分钟，可以再web.xml中修改

```java
session.getMaxInactiveInterval()	//获取超时时间
session.setMaxInactiveInterval(60*60*24); //设置超时时间
session.invalidate();  //session直接销毁
```

## session 原理

cookie中有一对名称值，存储的是是 JSESSION和Sessionid，用来标识session。也就是说session是基于cookie实现的。

![image-20201029181226691](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201029181226691.png)