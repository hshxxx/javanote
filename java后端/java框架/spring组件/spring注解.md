# 概述

![image-20201208105909398](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201208105909398.png)

# 容器

## AnnotationConfigApplicationContext

1. 作用：替代原来通过配置文件配置bean的方式。

2. 演示：

``` java
@Configuration
public class Config {
    //等同于在配置文件中注册bean，id默认是方法名，类型默认是方法的返回值类型
    @Bean 	//@Bean("别名") 
    public Person person(){
        return new Person(1,"张三");
    }
}
-----------------
//直接从配置类中获取bean
public static void main(String[] args) {
    ApplicationContext applicationContext = new AnnotationConfigApplicationContext(Config.class);
    Person bean = applicationContext.getBean(Person.class);
    Object person = applicationContext.getBean("person");
    System.out.println(bean);
    System.out.println("-------");
    System.out.println(person);
}
```

## 包扫描，排除和包括那些注解

1. filter类型
   - FilterType.ANNOTATION：指定加载使用某个注解的类对象
   - FilterType.ASSIGNABLE_TYPE：指定加载某个类对象
   - FilterType.CUSTOM：自定义过滤规则

```java
@Configuration
@ComponentScan(value = "com.annotation",
        includeFilters = {
//            @ComponentScan.Filter(type = FilterType.ANNOTATION,classes = {Controller.class}),
//            @ComponentScan.Filter(type = FilterType.ASSIGNABLE_TYPE,classes = {BookService.class}),
            @ComponentScan.Filter(type = FilterType.CUSTOM,classes = {MyFilterTest.class})
        },
        excludeFilters = {
//            @ComponentScan.Filter(type = FilterType.ANNOTATION,classes = {Repository.class}),
//            @ComponentScan.Filter(type = FilterType.ANNOTATION,classes = {Service.class})
        },useDefaultFilters = false) //不能使用默认加载规则，否则所有组件都会加载。
public class Config {
    //等同于在配置文件中注册bean，id默认是方法名，类型默认是方法的返回值类型
    @Bean
    public Person person(){
        return new Person(1,"张三");
    }
}
------------------
public class MyFilterTest implements TypeFilter {
    //MetadataReader：获取当前正在扫描的类信息
    //MetadataReaderFactory：获取其他类的信息
    //返回true表示符合，false相反
    public boolean match(MetadataReader metadataReader, MetadataReaderFactory metadataReaderFactory) throws IOException {
        //获取当前扫描类的注解信息
        AnnotationMetadata annotationMetadata = metadataReader.getAnnotationMetadata();
        //获取当前扫描类的类信息
        ClassMetadata classMetadata = metadataReader.getClassMetadata();
        //获取当前类资源
        Resource resource = metadataReader.getResource();

        String className = classMetadata.getClassName();
        System.out.println("当前正在扫描类为---》"+className);
		//只将类名中包含er的组件加载到容器中
        if(className.contains("er")){
            return true;
        }
        return false;
    }
}
```

## 单实例，多实例

```java
@Configuration
public class Config {
   	@Scope("prototype") //默认单实例，prototype 将bean设置为多实例
    @Bean 	
    public Person person(){
        return new Person(1,"张三");
    }
}
```

## @Lazy 注解 懒加载

只针对单实例。容器启动时不加载，第一次使用时才加载。

```java
@Configuration
public class Config {
    @Lazy
    @Bean 	
    public Person person(){
        return new Person(1,"张三");
    }
}
```

## @Condition 注解

符合条件的bean才会加载。需要实现Condition接口的实现类

```java
public class condition1 implements Condition {
    //ConditionContext：判断条件能使用的上下文（系统环境）
    //AnnotatedTypeMetadata：注释信息
    public boolean matches(ConditionContext context, AnnotatedTypeMetadata metadata) {
        if(1==0){
            return true;
        }
        return false;
    }
}
-------------------
@Configuration
public class Config {
	@Conditional(condition1.class)	//person1 不会被加载到容器中
    @Bean 	
     public Person person1(){
   	 	return new Person(2,"李四");
    }
}
```

## @import 注解

快速引入一个组件到容器中

```java
@Configuration
@Import({Book.class,School.class}) //引入Book和School两个类到容器中
public class Config {
    @Bean 	
     public Person person1(){
   	 	return new Person(2,"李四");
    }
}
```

快速引入多个组件到容器中，实现ImportSelector接口的实现类

```java
public class ImportSelectorTest implements ImportSelector {
    public String[] selectImports(AnnotationMetadata importingClassMetadata) {
        return new String[]{"com.annotation.bean.Book","com.annotation.bean.School"};
    }
}
-------------
@Configuration
@Import({ImportSelectorTest.class}) //引入ImportSelectorTest中包含的类到容器中
public class Config {
    @Bean 	
     public Person person1(){
   	 	return new Person(2,"李四");
    }
}
```

## FactoryBean 创建bean

实现声明FactoryBean接口的实现类

```java
public class BookFactoryBean implements FactoryBean<Book> {

    public Book getObject() throws Exception {
        return new Book();
    }

    public Class<?> getObjectType() {
        return Book.class;
    }

    public boolean isSingleton() {
        return true;	//true表示单实例，false表示多实例
    }
}
--------------
@Configuration
public class Config {
    @Bean
    public BookFactoryBean bookFactoryBean(){
        return new BookFactoryBean();	//调用getObject方法返回Book对象
    }
}
```

## 引入组件的方式

1. @Controller...
2. @Bean： 在配置类中使用
3. @Import ：引入一个或多个
4. 使用spring的FactoryBean



## bean的生命周期

bean创建-----bean初始化-----bean销毁

1. 自定义类中定义init和destory方法
   - bean创建：单实例在容器启动时创建，多实例在第一次使用时创建
   - bean初始化：创建完就执行
   - bean销毁：单实例在容器关闭时销毁，多实例需要自己手动销毁。

```java
@Component
public class Car {
	
	public Car(){
		System.out.println("car constructor...");
	}
	
	public void init(){
		System.out.println("car ... init...");
	}
	
	public void destory(){
		System.out.println("car ... detory...");
	}

}
-------------
@Configuration
public class Config {
    @Bean(initMethod="init",destoryMethod="destory")
    public Car car(){
        return new Car();	
    }
}
```

2. 声明 InitializingBean 和 DisposableBean 接口

```java
@Component
public class Cat implements InitializingBean,DisposableBean {
	
	public Cat(){
		System.out.println("cat constructor...");
	}

	@Override
	public void destroy() throws Exception {
		// TODO Auto-generated method stub
		System.out.println("cat...destroy...");
	}

	@Override	
	public void afterPropertiesSet() throws Exception {//属性赋值完后执行
		// TODO Auto-generated method stub
		System.out.println("cat...afterPropertiesSet...");
	}

}
```

3. 使用 JSR250，声明ApplicationContextAware 接口，使用 @PostConstruct 和 @PreDestroy 注解标注方法

```java
@Component
public class Dog implements ApplicationContextAware {
	
	//@Autowired
	private ApplicationContext applicationContext;
	
	public Dog(){
		System.out.println("dog constructor...");
	}
	
	//对象创建并赋值之后调用
	@PostConstruct
	public void init(){
		System.out.println("Dog....@PostConstruct...");
	}
	
	//容器移除对象之前
	@PreDestroy
	public void detory(){
		System.out.println("Dog....@PreDestroy...");
	}

	@Override
	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
		// TODO Auto-generated method stub
		this.applicationContext = applicationContext;
	}
}
```

4. 后置处理器：实现 BeanPostProcessor 接口的实现类。后置处理器会应用到容器中所有组件。

```java
@Component
public class MyBeanPostProcessor implements BeanPostProcessor {
	//在初始化之前调用
	@Override
	public Object postProcessBeforeInitialization(Object bean, String beanName) throws BeansException {
		// TODO Auto-generated method stub
		System.out.println("postProcessBeforeInitialization..."+beanName+"=>"+bean);
		return bean;
	}
	//在初始化之后调用
	@Override
	public Object postProcessAfterInitialization(Object bean, String beanName) throws BeansException {
		// TODO Auto-generated method stub
		System.out.println("postProcessAfterInitialization..."+beanName+"=>"+bean);
		return bean;
	}

}
```

## @Value属性赋值

1. 注解value

   使用方法：

   - 基本数据
   - SpEL：#{...}
   - ${...}：取出配置文件中的值

   ```java
   @Value("张三")
   private String name;
   @Value("#{20-10}")
   private Integer age;
   ------------
   @PropertySource(value = {"classpath:/dbproperties.properties"})
   @Configuration
   public class Config {
       @Bean
       public Car car(){
           return new Car();	
       }
   }
   public class Person{
       @Value("${jdbc.name}")
   	private String dbName;
   }
   ```

   

## 自动装配

1. @Autowired：自动装配。

   - @Autowired(required=false)：找不到不会报错，返回一个属性值为null的实例。
   - 可以标注在方法，构造器，属性，参数上。它们都是从容器中获取需要的组件。一般使用 @Bean + 方法上添加@Autowired，方法使用的组件都是从容器中拿。

2. @Qulifier("id")：指定组件的id，而不是默认使用类型名小写作为 id。

3. @Primary：容器中同类型bean有多个时，首选这个组件。和@Qulifier注解不能同时使用。

   ```java
   @Configuration
   public class Config {
   	@Primary
       @Bean
       public Car car(){
           return new Car();	
       }
       
       @Qulifier("car1")
       @Bean
       public Car car(){
           return new Car();	
       }
       
   }
   ```

4. Java自己的注解

   - @Resource：可以和@Autowired一样实现自动装配，不支持@Qulifier 和 @Primary
   - @Inject：可以和@Autowired一样实现自动装配，没有required=false 功能

5. xxxAware接口：自定义组件声明xxxAware接口后可以调用spring底层的组件。

   前面使用过的ApplicationContextAware接口

   ```java
   @Component
   public class Dog implements ApplicationContextAware {
   	...
       //spring会将ApplicationContext传过来
   	@Override
   	public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
   		// TODO Auto-generated method stub
   		this.applicationContext = applicationContext;
   	}
   }
   ```

6. @Profile 注解：根据环境选择性加载组件

   假如在开发过程中有 测试，开发，上线三种状态，这三种状态需要使用不同的数据库，可以用@Profile来标识组件，进而控制组件的加载。

   - 不使用@Profile的组件任何时候都加载
   - 加了环境标识的bean，只有这个环境被激活的时候才能注册到容器中。默认是default环境。
   - 写在配置类上，只有是指定的环境的时候，整个配置类里面的所有配置才能开始生效。

   ```java
   @PropertySource("classpath:/dbconfig.properties")
   @Configuration
   public class Config {
       //不使用@Profile的组件任何时候都加载
   	@Bean
   	public Yellow yellow(){
   		return new Yellow();
   	}
   	//开发过程的数据源
   	@Profile("test")
   	@Bean("testDataSource")
   	public DataSource dataSourceTest(@Value("${db.password}")String pwd) throws Exception{
   		ComboPooledDataSource dataSource = new ComboPooledDataSource();
   		dataSource.setUser(user);
   		dataSource.setPassword(pwd);
   		dataSource.setJdbcUrl("jdbc:mysql://localhost:3306/test");
   		dataSource.setDriverClass(driverClass);
   		return dataSource;
   	}
       //开发过程的数据源
       @Profile("dev")
       ...
       //上线后的数据源
       @Profile("prod")
       ...
       
   }
   ```

## Aop

在配置类上使用注解@EnableAspectJAutoProxy开启aop

```jav

```