# Maven

解决的问题：项目开发需要许多jar包，手动管理jar包非常麻烦，所以Maven出现了。

Maven除了能管理jar包之外，还支持项目的搭建（编译，打包，测试，部署），项目的分块开发。

## 下载和安装

1. 官网下载，本地解压
2. 添加环境变量
   - MAVEN_HOME：值就是Maven的解压目录，不是bin目录。
   - PATH：path中新建一个 %MAVEN_HOME%\bin 项。
3. 检验安装：在cmd中输入 mvn -v 若输出Maven版本信息则安装成功。

## Maven配置

1. 打开 conf目录下的settion.xml

   1. 配置 本地仓库，它用来存储jar包。

      ```xml
       <localRepository>H:\Maven\repository</localRepository>
      ```

   2. 配置jdk

      ```xml
      <profiles>
      	<profile>
      		<id>myjdk1.8</id>  
              <activation>  
                  <activeByDefault>true</activeByDefault>  
                  <jdk>1.8</jdk>  
              </activation>  
              <properties>    
                  <maven.compiler.source>1.8</maven.compiler.source>    
                  <maven.compiler.target>1.8</maven.compiler.target>    
                  <maven.compiler.compilerVersion>1.8</maven.compiler.compilerVersion>    
              </properties>
      	</profile>
      </profiles>
      <activeProfiles>
          <activeProfile>myjdk1.8</activeProfile>
      </activeProfiles>
      ```
      


### 仓库

存储依赖的地方，仓库中的每一个依赖都有唯一标识，可供java使用。

结构如图：

![image-20201030223230486](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201030223230486.png)

使用过的依赖都会自动加入到本地仓库中。

中央仓库：由官方维护，它存储了java大多数依赖。但从国内访问它较慢，所以使用国内的公共仓库。

公共仓库：非官方的仓库，著名的就是阿里云的Maven仓库，配置如下：

```xml
<mirror>
    <id>nexus-aliyun</id>
    <mirrorOf>central</mirrorOf>
    <name>Nexus aliyun</name>
    <url>http://maven.aliyun.com/nexus/content/groups/public></url>
</mirror>

```

私服：公司内部的仓库。

## idea中使用Maven

### 添加Maven

1. File | Settings | Build, Execution, Deployment | Build Tools | Maven 下配置这两项。

   ![image-20201030225525237](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201030225525237.png)

   安装目录和配置文件

### 新建Maven项目

有了Maven之后就不需要新建java还是java ee，直接新建Maven项目即可。

新建Maven的项目有两级结构：

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201030230146206.png" alt="image-20201030230146206" style="zoom: 80%;" />

GroupId：表示Maven所属的组

ArtifactId：表示Maven的项目名

Version：表示Maven项目的版本

这三个标识能唯一表示一个Maven项目，简称GAV。

### Maven项目目录结构

![image-20201031102857374](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201031102857374.png)

main文件夹存放所有主程序相关的文件。test文件夹存放所有的测试相关的文件。

test目录下的resources文件夹需要手动创建，并且把它设置成test resources root类型。

### 导入依赖

![image-20201031104154741](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201031104154741.png)

依赖通过在pom.xml中配置来引入Maven项目。依赖需要自己在网上找，或者自己从其他地方复制。

![image-20201031104822554](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201031104822554.png)

### 创建web项目

1. 设置项目打包类型

![image-20201031105020916](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201031105020916.png)

2. web依赖

   在 pom.xml 中需要导入jsp，Servlet 和 JSTL依赖，使项目具有web编译环境。

   ```xml
   <!--servlet依赖 -->
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>javax.servlet-api</artifactId>
       <version>3.0.1</version>
   </dependency>
   <!--jsp依赖 -->
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>jsp-api</artifactId>
       <version>2.0</version>
       <scope>provided</scope>
   </dependency>
   <!--jstl依赖 -->
   <dependency>
       <groupId>javax.servlet</groupId>
       <artifactId>jstl</artifactId>
       <version>1.2</version>
   </dependency>
   ```

3. 手动创建 webapp目录

   <img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201031111303606.png" alt="image-20201031111303606" style="zoom:80%;" />

   - 打开项目结构的Facet，将Maven项目设置为web类型，并且检查web.xml和webapp的地址是否设置正确。
   
     上面的deployment descriptions 设置web.xml的项目地址，下面的web resource directions设置webapp目录的地址。
   
     ![image-20201203183300185](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201203183300185.png)
   
   

### 部署web项目

新建tomcat服务器，将Maven项目部署到tomcat中。

跟随这个操作链：Run--edit configuration---tomcat ---local， 新建一个tomcat服务器。然后跟随这个操作链：deployement---add---artifact ，将Maven项目部署到服务器。



## 依赖的生命周期

设置依赖何时生效，何时失效。使用标签 scope。

**scope标签**的属性值：

1. **不写scope标签**，默认是compile，任何时间都有效。

2. **provided**：只在编译时有效，项目运行后不再有效。

   比如tomcat中有servlet，jsp依赖，如果项目中也配置了依赖，那么在运行时依赖会冲突，所以将pom.xml中的servlet，jsp依赖设置为provided。

3. **test**：只在测试时有用，适用于测试需要的jar包。

4. **runtime**：只在运行时用，编译时不用。

## Maven指令

指令：

1. clean：Maven项目编译后会生成一个target目录，里面存储了项目中所有的字节码文件，clean会清除target目录内所有内容。
2. compile：编译Maven项目。
3. package：打包Maven项目。根据pom.xml中package的设置决定打包后的格式。
4. install：把Maven项目打包，存储在本地仓库中。

使用：

1. 在idea右侧的Maven栏中点击指令
2. 在terminal中使用 mvn -指令  

## 私服

简介：

![image-20201031121230011](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201031121230011.png)

推荐使用nexus 仓库

![image-20201101100105611](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101100105611.png)

### 结构

私服可以理解为一个大的仓库，这个仓库里有许多不同的仓库。比如有**指向本地仓库的hosted仓库，指向远程仓库的proxy仓库，还有关联其他仓库的group仓库。**

### 配置

