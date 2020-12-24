# javaweb和tomcat

## JavaWeb

所有通过Java语言编写的，可以通过浏览器访问的程序的总称，叫JavaWeb。Javaweb是基于请求和相应来开发的，请求是浏览器向服务器发送数据，响应是服务器给浏览器发送数据。请求和响应是成对出现的。

## Web资源的分类

Web资源按照实现的技术和呈现的效果的不同，分为静态和动态资源。

静态资源：html, css, js, txt, mp4, jpg

动态资源：jsp页面，Servlet程序

## tomcat 启动方式

## 将web工程部署到tomcat

## 在idea中配置tomcat服务器

File | Settings | Build, Execution, Deployment | Application Servers 下添加tomcat

## idea中web工程目录结构

![image-20201025105506841](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201025105506841.png)

1. **src** 目录存放自己编写java源代码
2. **web**目录存放web工程所有的资源文件
3. **WEB-INF**目录是一个受服务器保护的目录，浏览器无法直接访问到此目录的内容，可以通过请求转发的形式访问到
4. **lib** 目录用来存储第三方的jar包，lib包一定要放在WEB-INF包下才能生效
5. **web.xml** 是整个动态web工程的配置部署描述文件，可以配置很多web工程的组件，比如servlet程序，filter过滤器，listener监听器等等

## idea中给web项目添加jar包

1. 手动给项目中WEB-INF目录下的lib文件夹中添加jar包
2. project structure --> libraries 下添加java类型的jar包，然后在artifacts下给某个需要jar包的module，点击fix 添加jar包

注意：

第一种方式：是将本地的jar包添加到模块中

第二种方式：是将某个模块的jar包添加到另一个模块中

## 注意

1. 在浏览器中输入http://ip:port/   访问的是tomcat文件夹中webapps下的ROOT工程
2. 在浏览器中输入http://ip:port/工程名   默认访问的是该工程下的index.html页面