# 微服务

[微服务概念提出者的论文](https://blog.csdn.net/qq_44358041/article/details/96483174)

## 入门

### 背景

本文介绍了网站应用的演进

随着互联网的发展，网站应用的规模不断扩大，常规的垂直应用架构已无法应对，分布式服务架构以及流动计算架构势在必行，亟需一个治理系统确保架构有条不紊的演进。

![image](http://dubbo.apache.org/imgs/user/dubbo-architecture-roadmap.jpg)

#### 单一应用架构

当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的数据访问框架(ORM)是关键。

#### 垂直应用架构

当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，提升效率的方法之一是将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的Web框架(MVC)是关键。

#### 分布式服务架构

当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的分布式服务框架(RPC)是关键。

#### 流动计算架构

当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。此时，用于提高机器利用率的资源调度和治理中心(SOA)是关键。

### 需求

本文介绍了 Dubbo 要解决的需求

![image](http://dubbo.apache.org/imgs/user/dubbo-service-governance.jpg)

在大规模服务化之前，应用可能只是通过 RMI 或 Hessian 等工具，简单的暴露和引用远程服务，通过配置服务的URL地址进行调用，通过 F5 等硬件进行负载均衡。

**当服务越来越多时，服务 URL 配置管理变得非常困难，F5 硬件负载均衡器的单点压力也越来越大。** 此时需要一个服务注册中心，动态地注册和发现服务，使服务的位置透明。并通过在消费方获取服务提供方地址列表，实现软负载均衡和 Failover，降低对 F5 硬件负载均衡器的依赖，也能减少部分成本。

**当进一步发展，服务间依赖关系变得错踪复杂，甚至分不清哪个应用要在哪个应用之前启动，架构师都不能完整的描述应用的架构关系。** 这时，需要自动画出应用间的依赖关系图，以帮助架构师理清关系。

**接着，服务的调用量越来越大，服务的容量问题就暴露出来，这个服务需要多少机器支撑？什么时候该加机器？** 为了解决这些问题，第一步，要将服务现在每天的调用量，响应时间，都统计出来，作为容量规划的参考指标。其次，要可以动态调整权重，在线上，将某台机器的权重一直加大，并在加大的过程中记录响应时间的变化，直到响应时间到达阈值，记录此时的访问量，再以此访问量乘以机器数反推总容量。

以上是 Dubbo 最基本的几个需求。

### 架构

Dubbo 架构

![dubbo-architucture](http://dubbo.apache.org/imgs/user/dubbo-architecture.jpg)

**节点角色说明**

| 节点        | 角色说明                               |
| ----------- | -------------------------------------- |
| `Provider`  | 暴露服务的服务提供方                   |
| `Consumer`  | 调用远程服务的服务消费方               |
| `Registry`  | 服务注册与发现的注册中心               |
| `Monitor`   | 统计服务的调用次数和调用时间的监控中心 |
| `Container` | 服务运行容器                           |

**调用关系说明**

1. 服务容器负责启动，加载，运行服务提供者。
2. 服务提供者在启动时，向注册中心注册自己提供的服务。
3. 服务消费者在启动时，向注册中心订阅自己所需的服务。
4. 注册中心返回服务提供者地址列表给消费者，如果有变更，注册中心将基于长连接推送变更数据给消费者。
5. 服务消费者，从提供者地址列表中，基于软负载均衡算法，选一台提供者进行调用，如果调用失败，再选另一台调用。
6. 服务消费者和提供者，在内存中累计调用次数和调用时间，定时每分钟发送一次统计数据到监控中心。

Dubbo 架构具有以下几个特点，分别是连通性、健壮性、伸缩性、以及向未来架构的升级性。

#### 连通性

- 注册中心负责服务地址的注册与查找，相当于目录服务，服务提供者和消费者只在启动时与注册中心交互，注册中心不转发请求，压力较小
- 监控中心负责统计各服务调用次数，调用时间等，统计先在内存汇总后每分钟一次发送到监控中心服务器，并以报表展示
- 服务提供者向注册中心注册其提供的服务，并汇报调用时间到监控中心，此时间不包含网络开销
- 服务消费者向注册中心获取服务提供者地址列表，并根据负载算法直接调用提供者，同时汇报调用时间到监控中心，此时间包含网络开销
- 注册中心，服务提供者，服务消费者三者之间均为长连接，监控中心除外
- 注册中心通过长连接感知服务提供者的存在，服务提供者宕机，注册中心将立即推送事件通知消费者
- 注册中心和监控中心全部宕机，不影响已运行的提供者和消费者，消费者在本地缓存了提供者列表
- 注册中心和监控中心都是可选的，服务消费者可以直连服务提供者

#### 健壮性

- 监控中心宕掉不影响使用，只是丢失部分采样数据
- 数据库宕掉后，注册中心仍能通过缓存提供服务列表查询，但不能注册新服务
- 注册中心对等集群，任意一台宕掉后，将自动切换到另一台
- 注册中心全部宕掉后，服务提供者和服务消费者仍能通过本地缓存通讯
- 服务提供者无状态，任意一台宕掉后，不影响使用
- 服务提供者全部宕掉后，服务消费者应用将无法使用，并无限次重连等待服务提供者恢复

#### 伸缩性

- 注册中心为对等集群，可动态增加机器部署实例，所有客户端将自动发现新的注册中心
- 服务提供者无状态，可动态增加机器部署实例，注册中心将推送新的服务提供者信息给消费者

#### 升级性

当服务集群规模进一步扩大，带动IT治理结构进一步升级，需要实现动态部署，进行流动计算，现有分布式服务架构不会带来阻力。下图是未来可能的一种架构：

![dubbo-architucture-futures](http://dubbo.apache.org/imgs/user/dubbo-architecture-future.jpg)

**节点角色说明**

| 节点         | 角色说明                               |
| ------------ | -------------------------------------- |
| `Deployer`   | 自动部署服务的本地代理                 |
| `Repository` | 仓库用于存储服务应用发布包             |
| `Scheduler`  | 调度中心基于访问压力自动增减服务提供者 |
| `Admin`      | 统一管理控制台                         |
| `Registry`   | 服务注册与发现的注册中心               |
| `Monitor`    | 统计服务的调用次数和调用时间的监控中心 |





# 分布式

**分布式系统是由若干独立计算机的集合，这些计算机对于用户来说就像单个系统。**

分布式系统是由一组通过网络进行通信，为了完成共同的任务而协调工作的计算机节点组成的系统。分布式系统的出现是为了用廉价的、普通的机器完成单个计算机无法完成的计算、存储任务。其目的就是利用更多的机器，处理更多的数据。

### 背景

本文介绍了网站应用的演进

随着互联网的发展，网站应用的规模不断扩大，常规的垂直应用架构已无法应对，分布式服务架构以及流动计算架构势在必行，亟需一个治理系统确保架构有条不紊的演进。

![image](http://dubbo.apache.org/imgs/user/dubbo-architecture-roadmap.jpg)

#### 单一应用架构

当网站流量很小时，只需一个应用，将所有功能都部署在一起，以减少部署节点和成本。此时，用于简化增删改查工作量的数据访问框架(ORM)是关键。

#### 垂直应用架构

当访问量逐渐增大，单一应用增加机器带来的加速度越来越小，提升效率的方法之一是将应用拆成互不相干的几个应用，以提升效率。此时，用于加速前端页面开发的Web框架(MVC)是关键。

#### 分布式服务架构

当垂直应用越来越多，应用之间交互不可避免，将核心业务抽取出来，作为独立的服务，逐渐形成稳定的服务中心，使前端应用能更快速的响应多变的市场需求。此时，用于提高业务复用及整合的分布式服务框架(RPC)是关键。

#### 流动计算架构

当服务越来越多，容量的评估，小服务资源的浪费等问题逐渐显现，此时需增加一个调度中心基于访问压力实时管理集群容量，提高集群利用率。此时，用于提高机器利用率的资源调度和治理中心(SOA)是关键。

# RPC

RPC【remote procedure call】远程过程调用，是一种进程间通信方式。它是一种思想，而不是规范。允许异地程序之间互相调用，且不用显式地编写其他代码，执行时和本地方法调用一样。

RPC有两个核心模块：通讯，序列化

![img](https://upload-images.jianshu.io/upload_images/7143349-9e00bb104b9e3867.png?imageMogr2/auto-orient/strip|imageView2/2/format/webp)

![image-20201220171747023](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201220171747023.png)

博客：https://www.jianshu.com/p/2accc2840a1b

# Dubbo

高性能、轻量级的开源Java RPC框架，它提供了三大核心能力：面向接口的远程方法调用，智能容错和负载均衡，以及服务自动注册和发现。

dubbo 只是一个RPC框架，它的竞品还有spring cloud，而springcloud是一个生态，比dubbo要强大的多。

博客：[dubbo和zookeeper关系](https://www.cnblogs.com/iisme/p/10620125.html)

# ZooKeeper

ZooKeeper可以替代dubbo框架中的注册中心，否则dubbo只适合实现点对点通信，而负载均衡和高可用的 实现会很麻烦。

![dubbo-architucture](http://dubbo.apache.org/imgs/user/dubbo-architecture.jpg)

[博客1：zookeeper应用场景](https://zhuanlan.zhihu.com/p/59669985)

[博客2：zookeeper应用场景](https://blog.csdn.net/liuming690452074/article/details/103962911)

# 不用zookeeper

## provider

定义服务接口

```java
public interface DemoService {
    String sayHello(String name);
}
```

在服务提供方实现接口

```java
public class DemoServiceImpl implements DemoService {
    @Override
    public String sayHello(String name) {
        return "Hello " + name;
    }
}
```

用 Spring 配置声明暴露服务

provider.xml（省略了beans标签的各种属性）

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>

    <!-- 当前项目在整个分布式架构里面的唯一名称，用于计算依赖关系 -->
    <dubbo:application name="helloworld-app"  />

    <!--dubbo这个服务所要暴露的服务地址所对应的注册中心，N/A为不使用注册中心-->
    <dubbo:registry address="N/A"/>

    <!--当前服务发布所依赖的协议；webserovice、Thrift、Hessain、http-->
    <dubbo:protocol name="dubbo" port="20880"/>

    <!--服务发布的配置，需要暴露的服务接口-->
    <dubbo:service interface="com.st.DemoService"
                   ref="demoService"/>

    <!--bean的定义-->
    <bean id="demoService" class="com.st.DemoServiceImpl"/>

</beans>
```

加载 Spring 配置

```java
public class Provider {

    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("provider.xml");
        context.start();
        System.in.read(); // 按任意键退出
    }
}
```



## consumer

consumer.xml

```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans>

    <!-- 消费方应用名，用于计算依赖关系，不是匹配条件，不要与提供方一样 -->
    <dubbo:application name="consumer-of-helloworld-app"/>

    <dubbo:registry address="N/A"/>

    <!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
    <dubbo:reference id="demoService" interface="com.st.DemoService"
                     url="dubbo://localhost:20880/com.st.DemoService"/>

</beans>
```

加载Spring配置，并调用远程服务

```java
public class Consumer {

    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext("consumer.xml");
        context.start();
        // 获取远程服务代理
        DemoService demoService = (DemoService)context.getBean("demoService");
        // 执行远程方法
        String hello = demoService.sayHello("world");
        // Hello world
        System.out.println( hello );
    }
}
```

这就是**典型的点对点的服务调用**。当然我们为了高可用，可以在consumer.xml中配置多个服务提供者，并配置响应的负载均衡策略

配置多个服务调用者在comsumer.xml的dubbo:reference标签的url属性中加入多个地址，中间用分号隔开即可配置**负载均衡策略**在comsumer.xml的dubbo:reference标签中增加loadbalance属性即可，值可以为如下四种类型

1. RoundRobin LoadBalance，随机，按权重设置随机概率。
2. RoundRobin LoadBalance，轮询，按公约后的权重设置轮询比率。
3. LeastActive LoadBalance，最少活跃调用数，相同活跃数的随机，活跃数指调用前后计数差。
4. ConsistentHash LoadBalance，一致性 Hash，相同参数的请求总是发到同一提供者。

```xml
<dubbo:reference id="demoService" interface="com.st.DemoService"
                 url="dubbo://192.168.11.1:20880/com.st.DemoService;
                 dubbo://192.168.11.2:20880/com.st.DemoService;
                 dubbo://192.168.11.3:20880/com.st.DemoService"
                 loadbalance="roundrobin"/>
```

## 问题

1. 当provider增加节点时，需要手动修改配置文件
2. 当provider某个节点失效了，consumer不能感知到还会向该节点发送请求，需要手动取消该节点。

这两个问题都揭示着一个问题：dubbo框架需要一个可以自动应对系统变动的组件。

# 用zookeeper

## 修改配置

要使用注册中心，只需要**将provider.xml和consumer.xml更改为如下**

```xml
<!--<dubbo:registry address="N/A"/>-->
<dubbo:registry protocol="zookeeper" address="192.168.11.129:2181"/>
```

如果zookeeper是一个集群，则多个地址之间用逗号分隔即可

```xml
<dubbo:registry protocol="zookeeper" address="192.168.11.129:2181,192.168.11.137:2181,192.168.11.138:2181"/>
```

**把consumer.xml中配置的直连的方式去掉**

```xml
<!-- 生成远程服务代理，可以和本地bean一样使用demoService -->
<!--<dubbo:reference id="demoService" interface="com.st.DemoService"-->
                 <!--url="dubbo://localhost:20880/com.st.DemoService"/>-->


<dubbo:reference id="demoService" interface="com.st.DemoService"/>
```

## zookeeper如何保存信息

启动上面服务后，我们观察zookeeper的根节点多了一个dubbo节点及其他，图示如下

![图解Dubbo和ZooKeeper是如何协同工作的？](https://www.javazhiyin.com/wp-content/uploads/2019/01/java7-1548405142.png)

最后一个节点中192.168.1.104是小编的内网地址，你可以任务和上面配置的localhost一个效果，大家可以想一下我为什么把最后一个节点标成绿色的。没错，最后一个节点是临时节点，而其他节点是持久节点，这样，当服务宕机时，这个节点就会自动消失，不再提供服务，服务消费者也不会再请求。如果部署多个DemoService，则providers下面会有好几个节点，一个节点保存一个DemoService的服务地址

其实一个zookeeper集群能被多个应用公用，多个开源项目中都应用到了 ZooKeeper，例如 HBase, Spark, Flink, Storm, Kafka, Dubbo 等等。如小编Storm集群和Dubbo配置的就是一个zookeeper集群，为什么呢？因为不同的框架会在zookeeper上建不同的节点，互不影响。如dubbo会创建一个/dubbo节点，storm会创建一个/storm节点，如图

![图解Dubbo和ZooKeeper是如何协同工作的？](https://www.javazhiyin.com/wp-content/uploads/2019/01/java7-1548405142-1.png)