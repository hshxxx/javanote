# Spring框架的概述

- 概述
  - Spring是一种分层的 Java EE开源框架，以Ioc 和 AOP为内核，提供了表现层spring MVC 和持久层 spring jdbc 以及业务层事务管理等众多企业级应用技术，还能整合第三方插件和类库，是主流的Java EE开发框架
- spring 的优势
  - 方便解耦，简化开发
    - 通过spring 提供的 IoC容器，可以将对象见的依赖关系交给 spring进行控制，避免硬编码所造成的过度程序耦合。用户也不必再为单例模式类、属性文件解析等底层的需求编写代码，更专注于上层应用开发
  - AOP 编程的支持
    - 通过 spring AOP 功能，方便进行面向切面的编程，许多不容易用传统 OOP 实现的功能可以通过 AOP轻松应对
  - 声明事务的支持
    - 可以将我们从单调烦闷的事务管理代码中解脱出来，通过声明式方式灵活的进行事务的管理，提高开发效率和质量
  - 方便程序的测试
    - 可以用非容器依赖的编程方式进行几乎所有的测试工作，测试不再是昂贵的操作，而是随手拈来
  - 方便集成各种优秀框架
    - spring 可以降低对各种框架的使用难度，提供对各种优秀框架的直接支持
  - 降低Java EE API 的使用难度
    - Spring 对Java EE API （如 JDBC,，Java mail，远程调用等）进行了薄薄的封装，是这些API的使用难度大大降低
  - Java 源码是经典学习范例
    - spring 源码设计精妙，结构清晰，体现着作者对Java设计模式灵活运用以及对Java技术的造诣
- spring 结构图
  - ![img](https://ss0.bdstatic.com/70cFuHSh_Q1YnxGkpoWK1HF6hhy/it/u=1708302516,3787760156&fm=26&gp=0.jpg)
  - 一个绿色方框就是一个模块，黑色方框代表所在模块的功能由那些 jar包导入来实现
- spring配置文件的书写需要插件提示

## 插件安装

- 概述：为了方便书写spring配置文件，需要在开发工具中引入spring的配置文件

## IOC控制反转 和 DI依赖注入

- IOC控制反转
  - **控制**：**资源的获取方式**有主动和被动两种，主动的情况下，需要什么资源自己创建即可，被动情况下，资源的获取交给一个容器来创建和设置，需要什么引用即可
  - 容器：管理所有的组件（有功能的类），一个类受容器管理，那么他的service也受容器管理，容器可以自动探查出哪些类需要用到另一个类，容器帮我们创建这个类的对象，并把它传送给需要的类
  - **反转：主动的new对象 变为 被动的接受资源**
- DI依赖注入
  - 容器能知道哪个组件（类）运行的时候需要另外一个组件（类），然后容器通过反射的原理，将容器中准备好的组件注入到它需要的地方

## helloword 流程

- 使用框架时的编写流程：导包---》 写配置---》测试

  - 导包

    - Beans , core, context, expression, 还有日志包 commons-logging ，没有日志包会报错

  - 写配置

    - spring的配置文件中集合了 spring 的 IOC 容器管理的所有组件
    - 创建一个spring bean configuration File XML文件，用来配置spring，这个文件中存储所有bean对象

  - 测试

    - spring bean configuration file XML 文件中 ： 

      - ```xml
        <beans xmlns="http://www.springframework.org/schema/beans"
        	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        	xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
            
        	<!--beans 标签下包含众多bean标签；bean中有id和class属性，id存这个bean的名字，class存这个bean所属类；property标签 设置属性 -->
        	<bean id="person1" class="com.hsh.springtest.bean.Person">
        			<property name="last_name" value="张三"></property>
        			<property name="age" value="19"></property>
        			<property name="gender" value="男"></property>
        	</bean>
        </beans>
        ```

    - test 测试类

      - ```java
        ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("ioc.xml");
        Object p = ioc.getBean("person1");
        System.out.println(p);	//能输出person1
        ```

- 注意事项

  - 类路径： 
    - 对于java项目来说，src源码包开始的路径成为类路径的开始，在文件夹中就是bin文件夹下。
    - 对于java web 项目来说，WEB-INF下的classes包开始的路径是类路径的开始
  - 先导包再创建配置文件
  - bean类被加入到配置文件中后，会显示s标记
  - 如果把spring的配置文件放到了conf文件夹下，那么记得给conf建立路径，这样

- 细节 

  - ApplicationContext 是IOC容器的接口，用它来获取spring的配置文件
  - 容器中对象的创建在加载配置文件时就已经创建好了，而不是调用的时候才创建
  - 同一个组件在IOC容器中是单实例的，即使ID重复也只保存一个
  - IOC容器在创建这个组件的对象时，property标签会通过**该对象的setter给属性赋值**
  - 容器中的name属性的值由getter方法中get后的名字决定，所以getter和setter方法最好自动生成，如果有**新添加的属性**时，**务必及时更新getter和setter方法**，否则在容器中不能正确给属性赋值

# IOC的相关实验



### 1.根据bean类型从IOC容器中获取bean的实例

```java
@Test
public test(){
    Person p = ioc.getBean(Person.class);
  	//当Person类的对象在配置文件中是唯一的情况下可以找到，否则报 NotUniquException
    Person p = ioc.getBean("person1", Person.class);
    //在容器中找Person类的id为person1的javabean
}
```

### 2. 通过构造器为bean的属性赋值

```xml
<bean>
    <constructor-arg name="last_name" value="张三" type="java.lang.String" index="0"> </constructor-arg>
    <!--type 属性用来标记这个属性的所属类型； index 属性来告诉容器这个标签定义的是第几个参数，当方法重载时有用 -->
</bean>
```

### 3. 通过p名称空间为bean赋值

- 名称空间：通过为标签加前缀来区分xml中重复的标签。
  - 比如在一个bean标签中如果同时有 <name> 人名 </name> 会和 <name> 书名</name>出现，那么容器不能分辨出这两个name，所以可以通过名称空间解决，如 <p: name> 人名 </p: name>  和 <b: name> 书名 </b: name>
  - 使用前需要导包

### 4. 正确的为各种属性赋值

- Person 类

  - ```java
    public class Person{
        private String last_name;
    	private Integer age;
    	private String gender;
    	
    	private Car car;
    	private List<Book> books;
    	private Map<String,Object> map;
        private Properties properties;
    }
    ```

- 为引用对象赋 null 值：在property标签内用标签 <null/>赋值，斜杠在后面

  - 标签： null/ 

  - ```xml
    <bean id="person2" class="com.hsh.springtest.bean.Person">
        <property id="last_name">
            <null/>   
        </property>
    </bean>
    ```

- 为对象中引用的其他类对象赋值

  - 在XML文件中引用别的类对象

    - ```xml
      <bean id="car1" class="com.hsh.springtest.bean.Car">
      	<property name="band" value="bmw"></property>
      </bean>
      <bean id="person2" ref="car1">
      </bean>
      ```

  - 自己赋值

    - ```xml
      <bean id="person2" class="com.hsh.springtest.bean.Person">
        	...
          <property name="car">
          	<bean class="com.hsh.springtest.bean.Car">
             		<property name="band" value="benz"></property>
             </bean>
          </property>
      </bean>
      ```

- 为存储Book类型的 list集合赋值

  - 标签： list , ref ; 属性 ：bean

  - ```xml
    <bean id="bookx" class="com.hsh.springtest.bean.Book">
    	<property name="book_name" value="shuihuzhuan"></property>
        <property name="author" value="shinaian"></property>
    </bean>
    
    <bean>
        ...
    	<property name="books">			<!--这一步相当于 new ArrayList<>() -->
        	<list>
            	<bean class="com.hsh.springtest.bean.Book" p:book_name="xiyouji"></bean>
                <!--通过名称空间引用 -->
                <ref bean="bookx"/>		<!--引用配置文件中已存在的book对象 -->
            </list>
        </property>
    </bean>
    ```

  - 每一个对象都要用 bean标签，list集合就使用list标签

  - bean 标签只有写在最外面时，他的ID才能被识别，如果像上面的list标签中的bean标签即使有ID也不能被外界所引用

- 为对象中的map集合赋值

  - 标签 ： map, entry 属性：key ,value, value-ref

  - ```xml
    <bean id="person3" class="com.hsh.springtest.bean.Person">
    	<map>
        	<entry key="key1" value="zhangsan"></entry>
            <entry key="key2" value-ref="bookx"></entry>
       		<entry key="key3">
            	<bean class="com.hsh.springtest.bean.Car">
                	<property name="band" value="audi"></property>
                </bean>
            </entry>
        </map>
    </bean>
    ```

- 为对象的 properties对象赋值

  - 标签：props 属性： key

  - ```xml
    <bean>
    	...
        <!--properties文件中存储键值对，而且都是字符串类型，所以value直接写在标签之间 -->
        <property name="properties">
        	<props key="username">root</props>
            <props key="password">123456</props>
        </property>
        
    </bean>
    ```

- 在 util名称空间创建集合类型的bean，方便被引用，此处以map为例

  - 需要使用util 名称空间，要导jar包

  - 标签 ： util : map  ,entry 属性：key ,ref
  
    ```xml
    <util:map id="myMap">			<!--这一步相当于new map<>() -->
    	<entry key="name">zhangsan</entry>
        <entry key="age">22</entry>
        ...
    </util:map>
    <bean id="person3" class="com.hsh.springtest.bean.Person">
    	<property name="map" ref="myMap"></property>
        <!--引用已经被创建好的map对象 -->
  </bean>
    ```
  
  - 如果是map，那么键值对默认是string--object，如果是list 默认存储object对象，所有类型的对象都可以存储
  
    - ```xml
      <!--util:map里存储了list,Person,12,map 四种元素 -->
      <util:map>
      	<list></list>
          <bean class="com.hsh.springtest.bean.Person"></bean>
          <value>12</value>
          <ref bean="myMap"></ref>
      </util:map>
      ```
  
- 级联属性赋值：对象中某一个对象的属性被改变，比如改变Person类对象中Car对象的品牌属性

  - 对象. 属性 直接修改即可

  - ```xml
    <bean id="person2" class="com.hsh.springtest.bean.Person">
        <property name="car" ref="car1"></property>  <!--car1的品牌是bmw -->
    	<property name="car.band" value="byd"></property>  <!--将car1的品牌改为byd -->
    </bean>
    ```





### 5. 通过继承实现bean配置信息的重用

- 两个对象基本信息一致，但仍有部分不同，为了方便书写，可以通过属性 parent 来获取相同的信息，只是继承配置信息，而不是子父类关系

- ```xml
  <bean id="person4" calss="com.hsh.springtest.bean.Person">
      <property name="last_name" value="张三"></property>
      <property name="age" value="19"></property>
      <property name="gender" value="男"></property>
  </bean>
  <bean id="person5" parent="person4">
	<property name="last_name" value="李四"></property>
  </bean>
  ```
  

### 6. 通过abstract属性来创建模版bean

- ```xml
  <bean id="person"  class="com.hsh.springtest.bean.Person" abstract="true">
      <!--这个person对象只能被继承，不能被获取 -->
  	...
  </bean>
  <bean id="person6" parent="person">	
  	...
  </bean>
  ```

### 7.测试bean的作用域，分别创建单实例和多实例的bean

- ```xml
  <beans>
  	<bean id="car2" class="com.hsh.springtest.bean.Person"></bean>	
      <bean id="car3" class="com.hsh.springtest.bean.Person" scope="prototype"></bean>	<!--多实例-->
   
  </beans>
  ```

-  ```java
  ...
      Car c1 = ioc.getBean("car2");
  	Car c2 = ioc.getBean("car2");  //c1==c2 结果为true，因为他们是单实例的
  	Car cc1 = ioc.getBean("car3");
  	Car cc2 = ioc.getBean("car3"); 	//c1==c2 结果为false，因为他们是多实例的
  ```

- 单实例bean

  - 在加载配置文件时就创建好对象，多次引用同一个bean时不会生成其他对象，都是同一个对象

- 多实例bean

  - 只有获取容器中的bean时才创建对象，也就是说调用getBean方法时才创建对象



### 8. 配置文件中通过静态工厂方法创建的bean，实例工厂方法创建的bean，FactoryBean

- 工厂方法：不再配置文件中给属性赋值，而是调用某个类来给属性赋值

-  静态工厂：

  - 因为工厂类不需要创建对象，所以叫静态；
  - 对象类 对象 = 工厂类. 工厂类方法   ； 
  - 工厂类的静态方法完成属性的赋值

- 实例工厂：

  - 先构造一个工厂类对象，然后调用方法来给属性赋值
  - 工厂类  工厂类对象 = new 工厂类（参数） ；  
  - 对象类 对象 = 工厂类对象. 工厂类方法

- 静态工厂方法

  - ```xml
    <bean id="airplane1" class="com.hsh.springtest.factory.AirplanInstanceFactory" 
          factory-method="makePlane">
    	<constructor-arg value="航天飞机"></constructor-arg>
    </bean>
    ```

  - ```java
    public class AirplanInstanceFactory{
        public static Airplane makePlane(String name){
            Airplane ap = new Airplane();
            ap.set..
            ...
            return ap;
        }
    }
    ```

  - factory-method 来存储调用方法名， constructor-arg 来存储参数名

  - 只需要用class指明这个bean需要被哪个工厂类操作，并传递参数即可

- 实例工厂方法

  - ```xml
    <bean id="airplanInstanceFactory" class="com.hsh.springtest.factory.AirplanInstanceFactory">
    </bean>
    <bean id="airplane2" class="com.hsh.springtest.bean.Airplane"
          factory-bean="airplanInstanceFactory"
          factory-method="makePlane"
          >
        <constructor-arg value="宇宙飞船"></constructor-arg>
    </bean>
    ```

  - ```java
    public class AirplanInstanceFactory{
        public Airplane makePlane(String name){
            Airplane ap = new Airplane();
            ap.set..
            ...
            return ap;
        }
    }
    ```

  - 

  - 需要先创建一个工厂类的bean，然后创建一个需要的对象的bean，用factory-bean声明调用的是哪个工厂类bean，用factory-method方法声明调用的是工厂类bean的那个方法

- FactoryBean

  - FactoryBean 是一个接口，实现了FactoryBean接口的类是spring可以识别的工厂类，spring会自动的调用工厂方法创建实例

    - ```java
      public class FactoryBeanTest implements FactoryBean<Book>{	//这里原来是个泛型，此处设定好为book对象服务
      	
      	//返回创建的对象
      	@Override
      	public Book getObject() throws Exception {
      		// TODO Auto-generated method stub
      		Book book = new Book();
              book.setBook_name("abd");
              book.setAuthor("tom");
      		return book;
      	}
      	//返回创建的对象的类型，spring会自动调用这个方法来确认创建的对像是什么类型
      	@Override
      	public Class<?> getObjectType() {
      		// TODO Auto-generated method stub
      		return Book.class;
      	}
      	//判断当前对象是否是单例
      	@Override
      	public boolean isSingleton() {
      		// TODO Auto-generated method stub
      		return false;
      	}
      }
      ```

  - 想要使用这个接口，首先要创建接口的实现类，其次要在容器中配置这个接口实现类

    - ```xml
      <bean id="factbean1" class="com.hsh.springtest.bean.FactoryBeanTest"></bean>
      ```

  - FactoryBean 接口服务的对象在容器启动时不会创建，等到用getBean调用时才创建

  - 而且多次调用这个接口返回的对象是否相同，取决于FactoryBean 接口中isSingleton方法确定该对象是否是单例


### 9.创建带有生命周期的bean

- ```xml
  <bean id="book1" class="com.hsh.springtest.bean.Book"
  		destory-method="myDestroy" init-method="myInit"   
        >
       <!-- 自定义bean的初始化方法和销毁方法-->
       <!-- 初始化方法和销毁方法都不能带参数-->
  </bean>
  ```
```
  
- 单实例生命周期

  - 容器启动 ，执行启动方法   --->   执行初始化方法   --->   容器关闭，执行销毁方法

- 多实例生命周期

  - 调用getBean方法时，执行构造器和初始化方法   ----->   容器关闭不会影响多实例对象的生存

### 10. 测试bean的后置处理器： BeanPostProcessor

- spring有一个接口：后置处理器，可以在bean的初始化前后调用方法，相比较factoryBean是在创建和销毁时执行方法

- ```java
  public class BeanPostProcessorTest implements BeanPostProcessor{
  	
      //初始化方法之后调用，可以对bean进行操作并返回之后的结果
  	@Override
  	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
  		// TODO Auto-generated method stub
  		return null;
  	}
      
  	//初始化方法之前调用，可以对bean进行操作并返回之后的结果
  	@Override
  	public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
  		// TODO Auto-generated method stub
  		return bean;
  	}
  }
```

- 实现步骤：编写这个接口的实现类，然后将声明这个接口的实现类注册在容器中

  - ```xml
    <bean id="..." class="com.hsh.springtest.bean.BeanPostProcessorTest"></bean>
    ```

  - 容器启动，使用构造器 ---->  后置处理器before ----> 初始化方法 ----> 后置处理器after ---> 初始化完成

- 容器中注册了后置处理器之后，所有的bean，无论是否有初始化方法都会调用后置处理器的两个方法





### 11.引用外部属性文件

- 数据库连接池作为单实例非常合适，一个项目用一个连接池，连接池中管理很多连接，连接直接从连接池中拿。所以让spring帮我们创建连接池对象，并管理

  - 直接在容器中配置数据库信息

  - ```xml
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="root"></property>
        <property name="password" value="123456"></property>
        <property name="jdbcUrl" value="jdbc:mysql://localhost:3306"></property>
        <property name="driverClass" value="com.mysql.jdbc.Driver"></property>
    </bean>
    <!--这里的class是jar包中的一个类 -->
    ```

  - 将数据库配置放在dbconfig.properties文件中，在容器中引用properties文件。这里需要**context名称空间**

  - ```properties
    jdbc.username=root
    jdbc.password=123456
    jdbc.jdbcUrl=jdbc:mysql://localhost:3306
    jdbc.driverClass=com.mysql.jdbc.Driver
    ```

  - ```xml
    <context:property-placeholder  location="classpath:dbconfig.properties"/>
    <bean id="dataSource" class="com.mchange.v2.c3p0.ComboPooledDataSource">
        <property name="user" value="${jdbc.username}"></property>
        <property name="password" value="${jdbc.password}"></property>
        <property name="jdbcUrl" value="${jdbc.jdbcUrl}"></property>
        <property name="driverClass" value="${jdbc.driverClass}"></property>
    </bean>
    ```

  - 加 jdbc的原因是 username是spring中的一个关键字，如果直接用会出错。
  
  - 此处的 ${} 不是EL表达式，而是数据库的表达式

### 12. 基于XML的自动装配

- 仅限于对**自定义的类的属性有效**

- ```xml
  <!-- private Person person -->
  <bean id="person" class="com.hsh.springtest.bean.Person" autowire="default"></bean>
  ```

  - autowire：意思就是自动装配，它有以下四种属性值
    - default / no：不自动装配
    - byName：在XML文件中以属性名（person) 查找并装配
    - byType：在XML文件中以属性的类型（Person）查找并装配
    - constructor：在XML文件中先按照**有参构造器的参数类型**装配，没有就直接为组件装配null。如果按照类型找到多个，那么以**参数的名字**作为 id继续匹配

### 13.SqEL测试

全称spring expression lanauge，支持运行时查询，和jsp页面中的EL表达式类似。

- 使用方法：**#{...}** 

- 引用字面量

  ```xml
  <bean id="book1" class="com.hsh.springtest.bean.Book">
      <property name="price" value="#{12*2}"></property><!--输出24，而不是12*2 -->
  </bean>
  ```

- 引用某个对象的属性值

  ```xml
  <bean id="book1" class="com.hsh.springtest.bean.Book">
      <property name="price" value="#{book2.price}"></property><!-- 将xml中id为book2的price赋给它 -->
  </bean>
  ```

- 引用某个对象

  ```xml
  <bean id="person7" class="com.hsh.springtest.bean.Person">
      <property name="car" value="#{car}"></property><!--将xml中id为car的bean赋给person的属性car -->
  </bean>
  ```

- 调用静态方法：#{ T ( 全类名 ).静态方法( )}

  ```xml
  <bean id="person7" class="com.hsh.springtest.bean.Person">
      <property name="email" value="#{T(java.util.UUID).randomUUID().toStrng().subString(0,5)}"></property> 
  </bean>
  ```

- 调用实例方法

  ```xml
  <bean id="person7" class="com.hsh.springtest.bean.Person">
      <property name="name" value="#{person6.getName()}"></property><!--调用XML中person6的getName() -->
  </bean>
  ```

  

### 13. 通过注解分别创建 Dao, Service, Controller

- spring上有四个注解如下，将注解注册在容器中，就可以给组件使用注解，
  - @Controller：控制器，给控制器层的组件添加这个注解
  - @Service：业务层，给业务层的组件添加这个注解
  - @Repository：给数据库dao层添加这个注解
  - @Component：给不属于以上的组件添加这个注解
  
- 使用步骤：

  - 给组件添加注解----》 用context名称空间在容器中注册注解 ----》 导入 aop包，支持注解

  - ```xml
    <!--配置文件中注册注解 -->
    <!--这里的base-package指定扫描的基础包，把基础包及它下面所有的包中所有加了注解的类，扫描进容器 -->
    <context:component-scan base-package="com.hsh.springtest">
    	<!--将用Component注解标注的类放进容器 -->
        <context:include-filter type="annotation"
                                expression="org.springframework.stereotype.Component" />
        <!--将用Service注解标注的类排除 -->
        <context:exclude-filter type="annotation"
                                expression="org.springframework.stereotype.Service" />
    	<!--将Person类排除 -->
        <context:exclude-filter type="assignable"
                                expression="com.hsh.springtest.bean.Person" />
    </context:component-scan>
    
    ```

  - base-package ：属性指定那个包下使用注解能被识别，如果没有下面的include也可以使用

- 注解的使用

  - **@Repository("别名")** ： 给某个类加这个注解可以改变他在容器中的别名
  - **@Scope(value="prototype")** ：把这个类在容器中变为多实例的

- 注意

  - 使用了注解的类在容器中默认的 id是 **类名的首字母小写**，如BookServic 在容器中默认id为 bookService
  - 组件默认都是单例

### 14. 使用 @Autowired注解实现根据类型实现自动装配

- 通过使用注解，将组件放在容器中，以后需要那个组件直接用 @Autowired引用即可，不需要自己装配，这就是依赖注入DI

  - ```java
    void test(){
        ApplicationContext ac = new ClassPathXmlApplicationContext("ioc.xml");
        BookService bean = ac.getBean(BookService.class);
        System.out.println(bean.getBookName());		//西游记
    }
    
    @Service
    public class BookService {
    	@Autowired
    	private BookDao bookDao;
    	
    	public String getBookName() {
    		return bookDao.getName();
    	}
    }
    
    @Repository
    public class BookDao {
    	public String getName() {
    		return "西游记";
    	}
    }
    ```

- @Autowired原理：以 使用@Autowired 的 private BookService bookService； 为例

  - **先按照组件类型BookService** 去容器中找，若找到一个则 bookService = ioc.getBean(BookService.class)。
    - 若找到多个，则**按照变量名 bookService作为id** 在容器中匹配，因为BookService的默认id就是bookService，所以还能找到。
    - 若没找到，那么继续按照变量名来找。

- Qualifier注解

  - 如果变量声明为 private BookService bookService1，XML中有多个BookService 类型的bean，但没有ID为bookService1的bean，那么spring会找不到对应的bean。但是我们还想让spring按照类型来找，我们可以使用Qualifier注解。
  
  - 使用格式：
  
    ```java
    @Autowired
    @Qualifier("bookService")			//spring还会按照bookService去IOC容器中找
    private BookService bookService1;
    ```
  
- @Resource 也是自动装配，这是j2ee 中用来自动装配的注解，@Autowired 是spring框架的注解。@Resource注解其他的javaee框架也能认识，但@Autowired 离开了spring框架就不能用了，所以@Resource注解更具有扩展性。

- 注意事项

  - 使用 @Autowired 是默认找到组件后默认装配上，但也可以通过 @Autowired(required=false) 来取消自动装配，如果找不到组件就给对象属性赋值null，不会发送异常了。
  - @Autowired 注解可以添加到**构造器方法，普通方法，变量，注解**上，如果添加到方法上，那么在IOC容器启动时会默认把方法的参数注入。@Qualifier注解和@Autowired 注解使用范围一样大。

### 15. spring的单元测试

- 导包 ： spring-test-4.3.9.RELEASE.jar ，并构建路径否则系统检测不到

- 直接在 junit上加注解

  - ```java
    @ContextConfiguration(locations="classpath:applicationContext.xml")
    @RunWith(SpringJUnit4ClassRunner.class)
    class test2 {
    	@Autowired
    	BookService bookService;
        
    	@Test
    	void test() {
    		System.out.println(bookService.getBookName());
    	}
    }
    ```

  - ContextConfiguration(locations="classpath:applicationContext.xml") ：通过注解把配置文件引入

  - @RunWith(SpringJUnit4ClassRunner.class) ： 使用spring的test 代替junit

  - 直接用 @Autowired就可以引入组件

### 16. 泛型依赖注入

- 结构

  - BaseService < T > ：父类，下面两个子类
    - BookService extends BaseService < Book > ： 通过注解加入到容器中
    - UserService extends BaseService < User >： 通过注解加入到容器中
  - BaseDao < T >：父类，下面两个子类
    - BookDao extends BaseDao< Book >： 通过注解加入到容器中
    - UserDao  extends BaseDao< User>： 通过注解加入到容器中

- BaseService < T> 

  - ```java
    public class BaseService<T>{
        @Autowired
        private BaseDao<T> baseDao;
    
        public void save(){
            baseDao.save();
        }
    }
    ```

- BaseDao < T> 

  - ```java
    pubic abstract class BaseDao<T>{
        public abstract void save();
    }
    ```

- 如果调用 bookService.save()  那么保存的是book； 如果调用 userService.save() 那么保存的是user。

- 实现原理：以UserService，BaseService，BaseDao，UserDao为例。

  - 因为UserService对象用注解标注，而且它还继承了BaseService<User>，那么IOC容器在加载时还会将BaseService带上。因为BaseService中声明了BaseDao<User>，那么容器还会将继承了BaseDao的类加入到容器中，又因为UserDao的泛型是User，所以根据传输的泛型就能找到UserDao，继而在使用save方法时就可以调用UserDao的save方法了。

- spring框架不但会根据对象类型，对象名在容器中查找对象，还会根据泛型查找。

# IOC源码

1. IOC是一个容器
2. 容器启动的时候创建所有单实例对象
3. 直接从容器中获取对象

## spring IOC

需要清楚的问题：

1. IOC容器的启动过程？启动期间都做了什么（什么时候创建所有单实例bean）？
2. IOC是如何创建这些单实例bean，并如何管理？bean保存在哪里？

思路：从 helloworld开始逐步调试每个方法

1. org.springframework.context.support包下  ClassPathXmlApplicationContext 源码：

   ```java
   public ClassPathXmlApplicationContext(
   			String[] configLocations, boolean refresh, @Nullable ApplicationContext parent)
   			throws BeansException {
   		super(parent);
   		setConfigLocations(configLocations);
   		if (refresh) {
               //所有单实例会被创建
   			refresh();
   		}
   	}
   ```

2. refresh() 源码

   ```java
   @Override
   	public void refresh() throws BeansException, IllegalStateException {	
   			synchronized (this.startupShutdownMonitor) {
   			// Prepare this context for refreshing.
   			prepareRefresh();
   
   			// Tell the subclass to refresh the internal bean factory.
                //spring解析xml配置文件，将要创建的所有bean的配置信息保存起来
   			ConfigurableListableBeanFactory beanFactory = obtainFreshBeanFactory();
   
   			// Prepare the bean factory for use in this context.
   			prepareBeanFactory(beanFactory);
   
   			try {
   				// Allows post-processing of the bean factory in context subclasses.
   				postProcessBeanFactory(beanFactory);
   
   				// Invoke factory processors registered as beans in the context.
   				invokeBeanFactoryPostProcessors(beanFactory);
   
   				// Register bean processors that intercept bean creation.
   				registerBeanPostProcessors(beanFactory);
   
   				// Initialize message source for this context.
                   //支持国际化功能
   				initMessageSource();
   
   				// Initialize event multicaster for this context.
   				initApplicationEventMulticaster();
   
   				// Initialize other special beans in specific context subclasses.
                   //留给子类的方法
   				onRefresh();
   
   				// Check for listener beans and register them.
   				registerListeners();
   
   				// Instantiate all remaining (non-lazy-init) singletons.
                   //初始化所有单实例bean的方法
   				finishBeanFactoryInitialization(beanFactory);
   
   				// Last step: publish corresponding event.
   				finishRefresh();
   			}
                   catch (BeansException ex) {
   				if (logger.isWarnEnabled()) {
   					logger.warn("Exception encountered during context initialization - " +
   							"cancelling refresh attempt: " + ex);
   				}
   
   				// Destroy already created singletons to avoid dangling resources.
   				destroyBeans();
   
   				// Reset 'active' flag.
   				cancelRefresh(ex);
   
   				// Propagate exception to caller.
   				throw ex;
   			}
   
   			finally {
   				// Reset common introspection caches in Spring's core, since we
   				// might not ever need metadata for singleton beans anymore...
   				resetCommonCaches();
   			}
   		}
   	}
   ```

3. finishBeanFactoryInitialization 的源码

   ```java
   protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
   		// Initialize conversion service for this context.
   		if (beanFactory.containsBean(CONVERSION_SERVICE_BEAN_NAME) &&
   				beanFactory.isTypeMatch(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class)) {
   			beanFactory.setConversionService(
   					beanFactory.getBean(CONVERSION_SERVICE_BEAN_NAME, ConversionService.class));
   		}
   
   		// Register a default embedded value resolver if no bean post-processor
   		// (such as a PropertyPlaceholderConfigurer bean) registered any before:
   		// at this point, primarily for resolution in annotation attribute values.
   		if (!beanFactory.hasEmbeddedValueResolver()) {
   			beanFactory.addEmbeddedValueResolver(strVal -> getEnvironment().resolvePlaceholders(strVal));
   		}
   
   		// Initialize LoadTimeWeaverAware beans early to allow for registering their transformers early.
   		String[] weaverAwareNames = beanFactory.getBeanNamesForType(LoadTimeWeaverAware.class, false, false);
   		for (String weaverAwareName : weaverAwareNames) {
   			getBean(weaverAwareName);
   		}
   
   		// Stop using the temporary ClassLoader for type matching.
   		beanFactory.setTempClassLoader(null);
   
   		// Allow for caching all bean definition metadata, not expecting further changes.
   		beanFactory.freezeConfiguration();
   
   		// Instantiate all remaining (non-lazy-init) singletons.
       	//初始化所有单实例bean
   		beanFactory.preInstantiateSingletons();
   	}
   
   ```

4. DefaultListableBeanFactory 类的 preInstantiateSingletons() 方法源码：

   ```java
   	@Override
   	public void preInstantiateSingletons() throws BeansException {
   		if (logger.isTraceEnabled()) {
   			logger.trace("Pre-instantiating singletons in " + this);
   		}
   
   		// Iterate over a copy to allow for init methods which in turn register new bean definitions.
   		// While this may not be part of the regular factory bootstrap, it does otherwise work fine.
           //1.拿到所有要创建的bean的名字
   		List<String> beanNames = new ArrayList<>(this.beanDefinitionNames);
   
   		// Trigger initialization of all non-lazy singleton beans...
           //2.按顺序创建bean
   		for (String beanName : beanNames) {
               //3.根据beanid获取到bean的定义信息
   			RootBeanDefinition bd = getMergedLocalBeanDefinition(beanName);
   			//判断bean是单例的，并且不是抽象的，不是懒加载
               if (!bd.isAbstract() && bd.isSingleton() && !bd.isLazyInit()) {
   				//是否是一个实现了factorybean接口的bean
                   if (isFactoryBean(beanName)) {
   					Object bean = getBean(FACTORY_BEAN_PREFIX + beanName);
   					if (bean instanceof FactoryBean) {
   						final FactoryBean<?> factory = (FactoryBean) bean;
   						boolean isEagerInit;
   						if (System.getSecurityManager() != null && factory instanceof SmartFactoryBean) {
   							isEagerInit = AccessController.doPrivileged((PrivilegedAction<Boolean>)
   											((SmartFactoryBean) factory)::isEagerInit,
   									getAccessControlContext());
   						}
   						else {
   							isEagerInit = (factory instanceof SmartFactoryBean &&
   									((SmartFactoryBean) factory).isEagerInit());
   						}
   						if (isEagerInit) {
   							getBean(beanName);
   						}
   					}
   				}
   				else {
                       //4.创建一个bean
   					getBean(beanName);
   				}
   			}
   		}
   
   		// Trigger post-initialization callback for all applicable beans...
   		for (String beanName : beanNames) {
   			Object singletonInstance = getSingleton(beanName);
   			if (singletonInstance instanceof SmartInitializingSingleton) {
   				final SmartInitializingSingleton smartSingleton = (SmartInitializingSingleton) singletonInstance;
   				if (System.getSecurityManager() != null) {
   					AccessController.doPrivileged((PrivilegedAction<Object>) () -> {
   						smartSingleton.afterSingletonsInstantiated();
   						return null;
   					}, getAccessControlContext());
   				}
   				else {
   					smartSingleton.afterSingletonsInstantiated();
   				}
   			}
   		}
   	}
   ```

5. AbstractBeanFactory 类的 getBean() 方法

   ```java
   @Override
   public Object getBean(String name) throws BeansException {
       //实际调用的是doGetBean()方法
       return doGetBean(name, null, null, false);
   }
   ```

6. AbstractBeanFactory 类的 doGetBean() 方法源码：

   ```java
   	protected <T> T doGetBean(final String name, @Nullable final Class<T> requiredType,
   			@Nullable final Object[] args, boolean typeCheckOnly) throws BeansException {
   
   		final String beanName = transformedBeanName(name);
   		Object bean;
   
   		// Eagerly check singleton cache for manually registered singletons.
           //1.先从已经注册的所有单实例bean中看有没有这个bean。第一次创建时肯定没有
   		Object sharedInstance = getSingleton(beanName);
   		if (sharedInstance != null && args == null) {
   			if (logger.isTraceEnabled()) {
   				if (isSingletonCurrentlyInCreation(beanName)) {
   					logger.trace("Returning eagerly cached instance of singleton bean '" + beanName +
   							"' that is not fully initialized yet - a consequence of a circular reference");
   				}
   				else {
   					logger.trace("Returning cached instance of singleton bean '" + beanName + "'");
   				}
   			}
   			bean = getObjectForBeanInstance(sharedInstance, name, beanName, null);
   		}
   		else {
   			// Fail if we're already creating this bean instance:
   			// We're assumably within a circular reference.
   			if (isPrototypeCurrentlyInCreation(beanName)) {
   				throw new BeanCurrentlyInCreationException(beanName);
   			}
   
   			// Check if bean definition exists in this factory.
   			BeanFactory parentBeanFactory = getParentBeanFactory();
   			if (parentBeanFactory != null && !containsBeanDefinition(beanName)) {
   				// Not found -> check parent.
   				String nameToLookup = originalBeanName(name);
   				if (parentBeanFactory instanceof AbstractBeanFactory) {
   					return ((AbstractBeanFactory) parentBeanFactory).doGetBean(
   							nameToLookup, requiredType, args, typeCheckOnly);
   				}
   				else if (args != null) {
   					// Delegation to parent with explicit args.
   					return (T) parentBeanFactory.getBean(nameToLookup, args);
   				}
                   else if (requiredType != null) {
   					// No args -> delegate to standard getBean method.
   					return parentBeanFactory.getBean(nameToLookup, requiredType);
   				}
   				else {
   					return (T) parentBeanFactory.getBean(nameToLookup);
   				}
   			}
   
   			if (!typeCheckOnly) {
   				markBeanAsCreated(beanName);
   			}
               try {
   				final RootBeanDefinition mbd = getMergedLocalBeanDefinition(beanName);
   				checkMergedBeanDefinition(mbd, beanName, args);
   
   				// Guarantee initialization of beans that the current bean depends on.
                   //拿到创建当前bean之前需要提前创建的bean。depends-on属性。如果有就循环创建
   				String[] dependsOn = mbd.getDependsOn();
   				if (dependsOn != null) {
   					for (String dep : dependsOn) {
   						if (isDependent(beanName, dep)) {
   							throw new BeanCreationException(mbd.getResourceDescription(), beanName,
   									"Circular depends-on relationship between '" + beanName + "' and '" + dep + "'");
   						}
   						registerDependentBean(dep, beanName);
   						try {
   							getBean(dep);
   						}
   						catch (NoSuchBeanDefinitionException ex) {
   							throw new BeanCreationException(mbd.getResourceDescription(), beanName,
   									"'" + beanName + "' depends on missing bean '" + dep + "'", ex);
   						}
   					}
   				}
                   // Create bean instance.
                   //创建bean实例
   				if (mbd.isSingleton()) {
   					sharedInstance = getSingleton(beanName, () -> {
   						try {
   							return createBean(beanName, mbd, args);
   						}
   						catch (BeansException ex) {
   							// Explicitly remove instance from singleton cache: It might have been put there
   							// eagerly by the creation process, to allow for circular reference resolution.
   							// Also remove any beans that received a temporary reference to the bean.
   							destroySingleton(beanName);
   							throw ex;
   						}
   					});
   					bean = getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
   				}
   				else if (mbd.isPrototype()) {
   					// It's a prototype -> create a new instance.
   					Object prototypeInstance = null;
   					try {
   						beforePrototypeCreation(beanName);
   						prototypeInstance = createBean(beanName, mbd, args);
   					}
   					finally {
   						afterPrototypeCreation(beanName);
   					}
   					bean = getObjectForBeanInstance(prototypeInstance, name, beanName, mbd);
   				}
   				else {
   					String scopeName = mbd.getScope();
   					final Scope scope = this.scopes.get(scopeName);
   					if (scope == null) {
   						throw new IllegalStateException("No Scope registered for scope name '" + scopeName + "'");
   					}
   					try {
   						Object scopedInstance = scope.get(beanName, () -> {
   							beforePrototypeCreation(beanName);
   							try {
   								return createBean(beanName, mbd, args);
   							}
   							finally {
   								afterPrototypeCreation(beanName);
   							}
   						});
   						bean = getObjectForBeanInstance(scopedInstance, name, beanName, mbd);
   					}
   					catch (IllegalStateException ex) {
   						throw new BeanCreationException(beanName,
   								"Scope '" + scopeName + "' is not active for the current thread; consider " +
   								"defining a scoped proxy for this bean if you intend to refer to it from a singleton",
   								ex);
   					}
   				}
   			}
               catch (BeansException ex) {
   				cleanupAfterBeanCreationFailure(beanName);
   				throw ex;
   			}
   		}
   
   		// Check if required type matches the type of the actual bean instance.
   		if (requiredType != null && !requiredType.isInstance(bean)) {
   			try {
   				T convertedBean = getTypeConverter().convertIfNecessary(bean, requiredType);
   				if (convertedBean == null) {
   					throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
   				}
   				return convertedBean;
   			}
               catch (TypeMismatchException ex) {
   				if (logger.isTraceEnabled()) {
   					logger.trace("Failed to convert bean '" + name + "' to required type '" +
   							ClassUtils.getQualifiedName(requiredType) + "'", ex);
   				}
   				throw new BeanNotOfRequiredTypeException(name, requiredType, bean.getClass());
   			}
   		}
   		return (T) bean;
   	}
   ```

7. DefaultSingletonBeanRegistry 类的 getSingleton方法

   ```java
   public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {
   		Assert.notNull(beanName, "Bean name must not be null");
   		synchronized (this.singletonObjects) {
               //从singletonObjects中将这个bean get出来
   			Object singletonObject = this.singletonObjects.get(beanName);
   			if (singletonObject == null) {
   				if (this.singletonsCurrentlyInDestruction) {
   					throw new BeanCreationNotAllowedException(beanName,
   							"Singleton bean creation not allowed while singletons of this factory are in destruction " +
   							"(Do not request a bean from a BeanFactory in a destroy method implementation!)");
   				}
   				if (logger.isDebugEnabled()) {
   					logger.debug("Creating shared instance of singleton bean '" + beanName + "'");
   				}
   				beforeSingletonCreation(beanName);
   				boolean newSingleton = false;
   				boolean recordSuppressedExceptions = (this.suppressedExceptions == null);
   				if (recordSuppressedExceptions) {
   					this.suppressedExceptions = new LinkedHashSet<>();
   				}
   				try {
                       //最终创建bean的方法
   					singletonObject = singletonFactory.getObject();
   					newSingleton = true;
   				}
                   catch (IllegalStateException ex) {
   					// Has the singleton object implicitly appeared in the meantime ->
   					// if yes, proceed with it since the exception indicates that state.
   					singletonObject = this.singletonObjects.get(beanName);
   					if (singletonObject == null) {
   						throw ex;
   					}
   				}
   				catch (BeanCreationException ex) {
   					if (recordSuppressedExceptions) {
   						for (Exception suppressedException : this.suppressedExceptions) {
   							ex.addRelatedCause(suppressedException);
   						}
   					}
   					throw ex;
   				}
   				finally {
   					if (recordSuppressedExceptions) {
   						this.suppressedExceptions = null;
   					}
   					afterSingletonCreation(beanName);
   				}
   				if (newSingleton) {
                       //将bean添加到singletonObject中，它是一个map
   					addSingleton(beanName, singletonObject);
   				}
   			}
   			return singletonObject;
   		}
   	}
   ```

8. DefaultSingletonBeanRegistry 类的 singletonObjects 是一个map对象

   ```java
   /** Cache of singleton objects: bean name to bean instance. */
   	//存储着创建好的所有的单实例bean，它是IOC容器的一个map，而整个IOC容器有许多map组成
   	private final Map<String, Object> singletonObjects = new ConcurrentHashMap<>(256);
   
   ```

   

# AOP

- AOP ： aspect oriented programing 面向切面编程，基于OOP的编程思想；
  - 指在程序运行期间，将**某段代码动态的切入到指定方法的指定位置然后运行**的编程方式，java有动态代理可以实现该功能
  - OOP : object oriented programing 面向对象编程

## 1. 动态代理

- 以计算器的方法在执行时需要输出日志作为例子，假如在计算器类的每一个方法都手动打印一个日志信息会很麻烦，所以使用动态代理，也就是说对计算机类生成一个代理对象，通过该代理对象去调用方法

- 代码

  - Calculator 接口

    - ```java
      public interface Calculator{
          public int add(int i, int j);
          ...
      }
      ```

  - Test类

    - ```java
      public void Test(){
      	Calculator calculator = new CalculatorImpl();	//创建一个接口的实现类
          Calculator proxy = CalculatorProxy.getProxy(calculator);	//创建动态代理
          proxy.add(1,2);
          //在打印加法的结果之前会打印日志信息
      }
      ```

  - CalsulatorProxy 类

    - 这是手动创建的一个Calculator的代理类CalculatorProxy，这个类为**传入的类**创建一个动态代理对象

    - ```java
      public class CalculatorProxy{	
          public static Calculator getProxy(final Calculator calculator){
              //loader 指向当前类的类加载器
              //interface 指向当前类的接口
              //h 方法执行器，帮被代理的目标对象执行目标方法
               ClassLoader loader = calculator.getClass().getClassLoader();
               Class<?>[] interfaces = calculator.getClass().getInterfaces();
               InvocationHandler h = new InvocationHander(){
                   //proxy：代理对象，给jdk用的，不要动
                   //method：当前要代理的对象的方法
                   //args：调用被代理对象的方法时需要的参数
                   @Override
                   public Object invoke(Object proxy, Method method, Object[] args) throws Throwable{
                       Object result = null;
                       try{
                           System.out.println("方法开始执行");
                           //利用反射执行目标方法
                           result = method.invoke(calculator,arg);
                           System.out.println("方法正常执行完成");
                       }catch(Exception e){
                           System.out.println("方法执行出现异常");
                       }finally{
                           System.out.println("方法最终执行结束");
                       }
                       return result;
                   }
               }
              
              Object proxy = Proxy.newProxyInstance(loader, interfaces, h);
              return proxy;
          }
      }
      ```

- 动态代理缺点

  - 写起来麻烦
  - 必须是有接口的类才能实现动态代理，否则代理对象和被代理对象没有关联

## 2. spring的 AOP

- 使用Java中的动态代理非常麻烦，而spring的AOP功能也能实现它的效果。它的底层就是动态代理，但spring的aop使用起来非常简单

- 图示：以Calculator为例  

  ![](C:\Users\xpty\Desktop\java后端\java框架\aop.jpg)

## 3. 基于注解的AOP的使用

- 导包：

  - spring-aspects-4.3.9.RELEASE.jar 这是spring的基础版的包，还有下面3个增强版的面向切面编程的包
  - com.springsource.net.sf.cglib-2.2.0.jar
    com.springsource.org.aopalliance-1.0.0.jar
    com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar

- 写配置

  - 将目标类和切面类（封装了通知方法（在目标方法执行前后的方法））加入到 IOC容器中

  - 首先要用**@Component注解**将切面类在容器中注册，然后用**@Aspect注解**告知容器它是切面类

  - 然后用**注解**告诉spring，切面类中的方法什么时候执行，有以下四个注解

    - **@Before ： 在目标方法执行之前执行，称为前置通知**
    - **@After：在目标方法结束之后执行，称为后置通知**
    - **@AfterReturning（）：在目标方法正常返回后执行，称为返回通知**
    - **@AfterThrowing（）：在目标方法抛出异常之后执行，称为异常通知**
    - **@Around ：环绕，称为环绕通知**

  - 演示

    - ```java
      try {		//带相应注解的切面类方法应该放置的位置
          @Before
      	method();
          @AfterReturning
      } catch (Exception e) {
          @AfterThrowing
      }finally {
      	@After
      }
      ```

  - 给切面类的方法添加注解，并写上切入点表达式

    - ```java
      public class logUtil{
          //指明 CaculatorImpl类的add方法在执行之前都调用logStart方法
      	@Before("execution(public int com.hsh.springtest.CaculatorImpl.add*(int,int))")
          public static void logStart() {
              System.out.println("方法开始执行");
          }
          //指明 CaculatorImpl类的所有方法在执行之前都调用logReturn方法
          @AfterReturning("execution(public int com.hsh.springtest.CaculatorImpl.*(int,int))")
          public static void logReturn() {
              System.out.println("方法返回结果");
          }
          ...
      }
      ```

    - **execution(...)** ：就是切入点表达式，它来指明某个普通类的一个方法或者所有方法都适用这个注解。同时括号内是该类的全类名，同时加上权限修饰符，返回值类型，还有参数

  - 最后需要在容器中开启基于注解的AOP功能：需要在dtd约束中打开aop名称空间

    - ```xml
      <aop:aspectj-autoproxy></aop:aspectj-autoproxy>
      ```

- 测试

  - Calculator 接口

    - ```java
      public interface Caculator {
      	public abstract int add(int i, int j);
      }
      ```

  - Calculator 接口的实现类

    - ```java
      @Component
      public class CaculatorImpl implements Caculator{
      	@Override
      	public int add(int i, int j) {
      		return i+j;
      	}
      }
      ```

  - logUtil 类：先用@Component加入容器，再用注解@Aspect告知spring这是个切面类

    - ```java
      @Aspect
      @Component
      public class logUtil {
      	@Before("execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))")
      	public static void logStart() {
      		System.out.println("方法开始执行");
      	}
      	@AfterReturning("execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))")
      	public static void logReturn() {
      		System.out.println("方法返回结果");
      	}
      	@AfterThrowing("execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))")
      	public static void logException() {
      		System.out.println("方法执行出现异常");
      	}
      	@After("execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))")
      	public static void logAfter() {
      		System.out.println("方法执行完毕");
      	}
      }
      ```

  - Test 类

    - ```java
      class test2 {
          @Test
          void test() {
              ClassPathXmlApplicationContext ioc = new ClassPathXmlApplicationContext("applicationContext.xml");
              //从容器中拿组件，而不是自己new，而且如果想用类型来查找组件，务必用接口类型获取
              Caculator bean = ioc.getBean(Caculator.class);
                      System.out.println(bean.getClass());
      
              int result = bean.add(1, 2);
              System.out.println(result);
          }
}
      ```
      
    - ```txt
      结果：
      class com.sun.proxy.$Proxy16
    方法开始执行
      方法执行完毕
    方法返回结果
      3
      ```
    
  
- 总结

  - 先将切面类和普通类用@Component加入容器
  - 然后用注解@Aspect声明切面类
  - 然后使用五个注解告诉spring何时对谁执行
  - 在容器中开启基于注解的aop功能

## 4. AOP 的细节

- 细节1--IOC容器中保存的是**组件的代理对象**
   - 上面的接口的实现类在容器中并不存在，而是它的代理对象，按照实现类.class 是找不到的，所以需要在容器中用接口来查询，代理对象是通过实现类的接口连接在一起的
   - 或者按照实现类的默认id在容器中找
   
- 细节2---cglib为没有接口的组件也可以创建代理对象

- 细节3----使用通配符的切入点的表达式的写法

  - execution(* *. *(..)) ： 表示任意权限修饰符，任意返回值类型，任意包下的任意类，而且参数不限制的方法
  - 权限修饰符不写就表示任意，不能用*来代替
  - 参数用 .. 就表示任意参数类型

- 细节4---普通通知的执行顺序

  - 正常执行： before ---> after ---> afterReturning
  - 异常执行： before--->  after ---> afterThrowing

- 细节5---JoinPoint获取目标方法的信息，用returning获取方法的结果，用throwing获取异常信息

  - ```java
    @Before(
        value="execution(public int com.hsh.springtest.impl.CaculatorImpl.(int,int))",
        returning="result")
    public static void logStart(JoinPoint joinPoint,Object result) {
        Object[] args = joinPoint.getArgs();		//获取参数数组
        String name = joinPoint.getSignature();		//获取方法签名
        System.out.println("方法"+name+"开始执行，参数是"+Arrays.asList(args)+"结果是"+result);
    }
    
    @AfterThrowing(value="execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))",
                  throwing="exception")
    public static void logException(Exception exception) {
        System.out.println("方法执行出现异常"+exception);
    }
    ```

  - result 和exception 可以直接用，但必须在注解和方法参数中中告诉spring

- 细节6---抽取可重用的切入点表达式，通过声明一个没有实现的返回void的空方法，并给方法添加@PointCut注解,然后引用即可

  - ```java
    @Pointcut("execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))")
    public void pointCut(){};
    
    @AfterThrowing(value="pointCut()",
                  throwing="exception")
    public static void logException(Exception exception) {
        System.out.println("方法执行出现异常"+exception);
    }
    ```

- 细节7 --- 环绕通知就是动态代理，不过他写起来更方便，在环绕通知上就可以实现其他四个注解的功能，如果只是想打印一些信息，而不是改变被代理对象，那么就用普通通知，如果**想要对被代理对象的方法做些改变，那么就用环绕通知**。

   - ```java
      @Around("execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))")
      public static Object logAround(ProceedingJoinPoint pjp) throws Throwable {
          Object[] args = pjp.getArgs();
          String name = pjp.getSignature().getName();
      
          Object proceed = null;
          try {
              System.out.println("环绕通知【"+name+"】方法开始执行");
              proceed= pjp.proceed(args);	//就是动态代理中的method.invoke()，帮我们执行方法
              System.out.println("环绕通知【"+name+"】方法正常执行完毕，返回结果");
      
          } catch (Exception e) {
              System.out.println("环绕通知【"+name+"】方法出现异常");
              throw new RuntimeException(e);//环绕通知的异常抛出去，否则外界不能知晓出现异常
          }finally{
              System.out.println("环置通知【"+name+"】方法结束执行");
          }
          return proceed;
      }
      ```
   ```
      
   - ```txt
      环绕通知【add】方法开始执行
      环绕通知【add】方法正常执行完毕，返回结果
      环置通知【add】方法结束执行
   ```

   - **ProceedingJoinPoint** ：就是java的动态代理中的 JoinPoint一样，但显而易见spring的环绕通知简单了许多

   - 环绕通知和普通通知一同执行时，环绕通知先执行

      - ```
         //通知顺序
         环绕通知[add]方法开始执行！
         方法开始执行
         环绕通知[add]方法正常执行结束！
         环绕通知[add]方法结束执行！
         方法执行结束
         方法正常执行后返回
         ===============
         普通前置
         {
         	try{
         		环绕前置
         		环绕执行：目标方法执行
         		环绕返回
         	}catch(){
         		环绕异常
         	}finally{
         		环绕后置
         	}
         }
         普通后置
         普通方法返回/方法异常
         =========
         新的执行顺序：（环绕前置---普通前置）---目标方法执行---环绕返回/异常--环绕后置---普通后置---普通返回/异常
         =======
         原因：环绕通知中的pjp.proceed(args)方法相当于动态代理的方法引用，只有当方法引用了之后才能触发各种通知，而环绕通知离得近，所以环绕通知先执行。环绕前置和普通前置会因为XML配置导致执行顺序不固定，但目标方法执行之后的通知顺序固定
         ```

   - 环绕通知和普通通知的执行顺序不同

      - 环绕通知：前置---返回---结束
      - 普通通知：前置---结束---返回

- 细节八---多个切面类的运行顺序

   - 默认是按照**切面类的首字母排序**，或者也可以用**注解@Order(1) 来标注**，按括号里面的数字顺序执行
   
   - 多个切面类执行的原理图：先执行的切面类后结束，后执行的切面类先结束
   
     ![image-20201125112830679](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201125112830679.png)

## 5. AOP 的应用

- AOP 加日志保存到数据库
- AOP 做权限验证
- AOP 做安全检查
- AOP 做事务控制

## 6. 基于配置的AOP的使用

- 步骤

  - 把切面类和普通类加入到 IOC容器中

    - ```xml
      <bean class="com.hsh.springtest.impl.CaculatorImpl"></bean>	
      <bean id="log1"class="com.hsh.springtest.log"></bean>
      ```

  - 指定切面类并规定前置，后置等通知

    - ```xml
      <aop:config>
          <!--config标签内都能引用的切入点表达式 -->
          <aop:pointcut expression="execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))" id="caculPoint"/>
          <!--指定切面类，并规定前置，后置等通知 -->
          <aop:aspect ref="log1">
              <aop:before method="logStart" pointcut-ref="caculPoint"/>
              <aop:after-returning method="logReturn" pointcut-ref="caculPoint" returning="result"/>
              <aop:after-throwing method="logException" pointcut-ref="caculPoint" throwing="exception"/>
              <aop:after method="logAfter" pointcut="execution(public int com.hsh.springtest.impl.CaculatorImpl.*(int,int))"/>
              <aop:around method="logAround" pointcut-ref="caculPoint" />
          </aop:aspect>
      </aop:config>
      ```

	-	普通的切面用注解，重要的用配置

## 7.AOP原理

AOP依赖动态代理实现，多个切面类就是多个代理的嵌套代理，嵌套在引用方法的地方。



# JdbcTemplate

实际应用时不用它

# 声明式事务

- 事务：一组不可分割的操作，要么都执行，要么都不要执行。
- 编程式事务：通过过滤器实现事务控制

## 事务的 helloworld

- 导包

  - 事务的实现利用的就是aop思想，所以jar包还是aop的那四个包
    - spring-aspects-4.3.9.RELEASE.jar 这是spring的基础版的包，还有下面3个增强版的面向切面编程的包
    - com.springsource.net.sf.cglib-2.2.0.jar
      com.springsource.org.aopalliance-1.0.0.jar
      com.springsource.org.aspectj.weaver-1.6.8.RELEASE.jar

- 写配置

  - ```xml
    <!--控制数据源 -->
    <bean id="transactionManager" class="org.springframework.jdbc.datasource.DataSourceTransactionManager">
        <property name="dataSource" ref="pooledDataSource"></property>
    </bean>
    ```

  - pooledDataSource 就是数据库的id，**控制数据源就是控制数据库源**

  - ```xml
    <!--用tx地址空间开启基于注解的事务控制模式 -->
    <tx:annotation-driven transaction-manager="transactionManager" />
    ```

  - 最后给需要事务控制的方法添加注解 @Transactional

- 测试

  - 加入带有注解 @Transactional的方法里面还有3个方法，只要有一个方法没有正确执行，那么其他2个方法都会失效，只有当3个方法都正确执行时才将对数据库的操作提交

## 事务的实现原理

```
1.获取数据库连接
2.设置为非自动提交
3.目标代码执行
4.正常提交 / 异常回滚
5.关闭数据库连接
```

## 事务的四个特性

1. 原子性：一组操作要么全部执行，要么不执行
2. 一致性：事务执行前后数据库的数据要一致正确，比如A转给B200块，结果A账户少了200但B账户没有多200
3. 隔离性：多个事务执行时互不影响
4. 持久性：只要事务成功执行，对数据的修改就是永久性的，不能因为其他意外而导致数据修改失效。

## 事务的细节

@Transaction( 事务设置)：

- isolation-Isolation：事务的隔离级别
- propagation-Propagation：事务的传播行为
- noRollbackFor-Class[]：哪些异常事务可以不回滚，直接指明异常的类型
- noRollbackForClassName-String[]：同上，但需要用全类名指定
- rollbackFor-Class[]：哪些异常需要回滚
- rollbackForClassName-String[]：同上，但它保存的是全类名
- readOnly-boolean：设置事务为只读事务，如果事务中的方法都是查询，就可以添加这个属性提高相应
- timeout-int：事务超出指定执行时长后自动终止并回滚，以秒为单位

## 哪些异常事务可以不回滚

- 概述

  - 异常分为
    - 编译时异常，要么try/catch，要么抛出，而且默认不回滚
    - 运行时异常，可以不用处理，默认回滚
  - noRollbackFor 就是让哪些本来不回滚的编译时异常，现在回滚
  - rollbackFor 就是让哪些本来回滚的运行时异常，现在不回滚

- 展示

  - ```java
    @Transactional(noRollbackFor{NullPointerException.class}) 
    //如果出现空指针异常，那么不回滚
    ```

## 事务的隔离级别

- 隔离级别：数据库系统必须具有隔离并发运行各个事务的能力，使他们不相互影响，一个事务与其他事务的隔离程度称为隔离级别，隔离级别越高，数据一致性就越好，但并发性越弱
- 数据库事务并发问题，以两个事务A和B为例
  - 脏读
    - A将某条数据的值从20改为30
    - B读取这条数据，获取30
    - A回滚，这条数据的值回到了20
    - B读取到的30就是一个无效的值
  - 不可重复读
    - A读取了某条数据的值为20
    - B级将这条数据的值改为30
    - A再次读取这条数据值为30，和第一次读取的结果不一致
  - 幻读
    - A读取了某个表中一部分数据
    - B向这个表中插入了新的行
    - A读取这个表时，多出来了几行
- 数据库的隔离级别
  - 读未提交
    - 允许A读取B未提交的修改
  - 读已提交
    - 要求A只能读取B已提交的修改
  - 可重复读
    - 确保A可以多次从一个字段中读取到相同的值，即A执行期间禁止其他事务对这个字段进行更新
  - 串行化
    - 确保A可以多次从一个表中读取到相同的行，在A执行期间，禁止其他事务对这个表进行添加，更新，删除操作。这可以避免任何并发问题，但效率低下

## 事务的传播行为

- 传播行为 = 事务的传播 + 事务的行为
  - 如果多个事务嵌套运行，那么子事务是否要和大事务共用一个事务
- 传播属性（前两个最常用）
  - **required**：如果有事务正在运行，当前方法可以选择加入这个事务，或者创建新的事务，在新的事务中运行
    - **坐别人的车**
  - **required_new**：当前方法必须创建新的事务，并在其中运行，如果有事务正在运行，应该将正在运行的事务挂起
    - **开新车**
  - supports：如果有事务在运行，当前的方法就在这个事务内运行，否则他可以不运行在事务中
  - not_supports：当前方法不应该运行在事务中，如果有运行的事务，将这个事务挂起
  - mandatory：当前的方法必须运行在事务中，如果没有正在运行的事务，就抛出异常
  - never：当前的方法不应该运行在事务中，如果有正在运行的事务，就抛出异常
  - nested：如果有事务运行，当前的方法就应该在这个事务的嵌套事务内运行，否则，就启动一个新的事务，并在他自己的事务内运行
- 注意
  - 任何事务崩溃，对于在这个崩溃事务之前的requied_new事务都不会崩溃，因为他已经执行完了
  - 如果是required，事务的属性都是继承大事务的，大事务要执行多久就执行多久
  - 本类方法的嵌套调用都在一个事务中











