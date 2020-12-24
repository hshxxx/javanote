# 概述

- 框架： 软件开发的一套解决方案，是一个半成品，不同的框架解决不同的问题，框架封装了很多技术细节
- java开发的三层架构
  - 表现层：展示数据
  - 业务层：处理业务需求
  - 持久层：数据库的交互
- 持久层技术解决方案
  - JDBC ： Java自带的与数据库交互的组件，使用非常繁琐，属于一种开发规范
    - 编写sql ---> 预编译 ---> 设置参数 ---> 执行sql --->  封装结果
  - spring的jdbc Template ：是一种工具类，对JDBC的封装
    - ORM(object relational mapping)：对象关系映射，把数据库表和实体类及其属性映射起来，继而操作实体类就可以操作数据库表
  - Hibernate：全自动全映射ORM框架，sql语句由框架编写，项目末期必须进行sql优化
    - java bean ---> 编写sql.....--->执行sql ---> 数据库记录 。hibernate封装了java bean 到数据库记录之间的过程
  - Mybatis框架：用Java编写的持久层框架，封装了JDBC，sql语句由开发人员编写
    - 相比与hibernate，mybatis允许开发人员自己编写sql语句，把sql语句写在配置文件中

# helloworld

- 利用ID从数据库中employee表中检索一条数据，用XML的方式实现

- 配置文件和类

  - lib 文件夹 ： 存储 jar包。记得要将lib文件下所有的jar包**构建路径**，Java在编译Java项目时能找到依赖的类

    - log4j.jar ：用来将数据输出到控制台上
    - mysql-connector-java-xxxx.jar ：用来将MySQL和 Java 连接起来
    - mybatis-xxxx.jar： MyBatis最基础的jar包

  - conf 文件夹 ： 存储 xml文件

    - log4j.xml ： 光有jar包不够，还要有xml文件才可以将数据输出到控制台上

    - EmployeeMapper.xml ：对实体的数据库表进行的操作

      - ```xml
        <!--以前的使用方法 -->
        ...  
        <mapper namespace="com.hsh.conf.EmployeeMapper">
        	<select id="selectEmp" resultType="com.hsh.bean.Employee">
        		select * from employee where id = #{id}
        	</select>
        </mapper>
        
        <!--现在使用接口来实现 -->
        ...
        <mapper namespace="com.atguigu.mybatis.dao.EmployeeMapper">
        <!-- namespace: 名称空间,以前是绑定要返回对象的类路径，现在是接口
        	 id : 唯一标识
        	 resultType :返回值类型，为了准确一般写上bean的全类名
        	 #{id}： 从传递过来的参数中取出ID值
         -->
        	<select id="getElmById" resultType="com.atguigu.mybatis.bean.Employee">
        		select id,last_name lastname,gender,email from Employee where id = #{id}
        	</select>
        </mapper>
        ```

    - mybatis-config.xml： 全局配置文件，配置了数据库环境和写好的 sql映射文件。 记得**配置路径**

      - ```xml
        ...
        <configuration>
        	<environments default="development">
        		<environment id="development">
        			<transactionManager type="JDBC" />
        			<dataSource type="POOLED">
        				<property name="driver" value="com.mysql.jdbc.Driver" />
        				<property name="url" value="jdbc:mysql://localhost:3306/mybatis" />
        				<property name="username" value="root" />
        				<property name="password" value="123456" />
        			</dataSource>
        		</environment>
        	</environments>
        	
        	<!-- 将写好的sql映射文件（EmployeeMapper.xml）一定要注册到全局配置文件（mybatis-config.xml）中 -->
        	<mappers>
                <!--EmployeeMapper文件和mybatis-config都在conf文件夹下 -->
        		<mapper resource="EmployeeMapper.xml" />		
        	</mappers>
        </configuration>
        ```

  - employee 类

  - ```java
    //bean类
    public class employee{
    	private Integer id;
        private String name;
        getter & setter & toString
    }
    ```

    

  - test 主类

  - ```java
    public class test {
    
    	@Test
    	public void test() throws IOException {	//用旧方法
    		String resource = "mybatis-config.xml";	//获取全局配置文件
    		InputStream inputStream = Resources.getResourceAsStream(resource);
    		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    		//根据全局配置文件获得sqlSeesionFactory
    		SqlSession openSession = sqlSessionFactory.openSession();
            //利用sqlSessionFactory获得sqlSession，代表和数据库的一次回话，它可实现增删改查操作，使用完要关闭
            
    		Employee employee = openSession.selectOne("com.hsh.conf.EmployeeMapper.selectEmp",1);
            //利用selectOne方法查找sql映射文件中ID为selectEmp的sql语句，并传参给语句
    		System.out.println(employee);
    		openSession.close();
            //使用完关闭sqlSession
    	}
    	
    	@Test
    	public void test1() throws IOException {	//新方法使用接口实现
    		String resource = "mybatis-config.xml";
    		InputStream inputStream = Resources.getResourceAsStream(resource);
    		SqlSessionFactory sqlSessionFactory = new SqlSessionFactoryBuilder().build(inputStream);
    		SqlSession openSession = sqlSessionFactory.openSession();
            //利用getMapper方法查找全局配置文件中的EmployeeMapper的sql映射文件
    		EmployeeMapper mapper = openSession.getMapper(EmployeeMapper.class);
            //调用接口中定义的getEmpById方法并给它传参，mybatis会自己生成一个接口的实现对象来实现查询
    		Employee elmById = mapper.getEmpById(2);
    		System.out.println(elmById);
    		openSession.close();
    	}
    
    }
    ```
  
- 总结

  - 将写好的sql映射文件（EmployeeMapper.xml）一定要注册到全局配置文件（mybatis-config.xml）中
  - 旧的方法是直接在相应的xml文件中调用sql语句，新的方法是写一个接口，接口中声明一个方法，这个方法名就是XML文件中sql语句的ID
  - SqlSession和connection一样都是非线程安全
  - mapper接口没有实现类，但是mybatis会为这个接口生成一个代理对象
  - **类路径**的作用是告诉jvm从哪些地方查找类，类路径中可以指定三种位置：文件夹、jar文件、zip文件

# 全局配置文件

- 首先引入dtd 约束，然后介绍各种标签

- dtd 约束

  - 电脑联网，直接输入网址即可，否则需要手动添加

    - ```xml
      <!-- 放在DOCTYPE中-->
      <!DOCTYPE configuration
       PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
       "http://mybatis.org/dtd/mybatis-3-config.dtd">
      ```

- 标签

  - **properties 标签**： 用来引入外部properties配置文件的内容，在XML文件中要放在configuration中**最上面**，它有两个属性

    - resource：用来引入类路径下资源

    - url ：用来引入网络资源或磁盘路径下资源

    - 实际应用：新建一个properties 文件，在该文件中编写数据库相应参数，在XML文件中引用

      - ```properties
        properties文件
        jdbc.driver:com.mysql.jdbc.Driver
        jdbc.url:jdbc:mysql://localhost:3306/mybatis
        jdbc.name:root
        jdbc.password:123456
        ```

      - ```xml
        <configuration>
        	<properties resource="dbconfig.properties"></properties>
        	<environments default="development">
        		<environment id="development">
        			<transactionManager type="JDBC" />
        			<dataSource type="POOLED">
        				<property name="driver" value="${jdbc.driver}" />
        				<property name="url" value="${jdbc.url}" />
        				<property name="username" value="${jdbc.name}" />
        				<property name="password" value="${jdbc.password}" />
        			</dataSource>
        		</environment>
        	</environments>
            <!--主配置文件中配置的sql映射文件 -->
        	<mappers>
        		<mapper resource="EmployeeMapper.xml" />
        	</mappers>
        </configuration>
        ```

  - **settings 标签**：包含很多重要的设置项

    - mapUnderscoreToCamelCase 属性 ：用来将下划线命名和驼峰命名映射到一起，

      - ```xml
        <setting name="mapUnderscoreToCamelCase" value="true"/>
        <!--如果数据库采用下划线命名某个列，而java中对应实体命名为驼峰命名，那么开启后实体和数据库中的列可以对应上，不至于出错 -->
        ```

  - **typeAliases 标签**：别名处理器，可以为Java中的类型起别名，别名不区分大小写

    - 第一种方式：用 typeAlias 标签单独起别名，它有两个属性

      - type : 指定要起别名的类型全类名； 默认别名就是类名小写，如employee

      - alias ：指定新的别名

      - ```xml
        ...
        <typeAliases>
        		<typeAlias type="com.hsh.bean.Employee" alias="emp" />
        </typeAliases>
        ...
        ```

      - ```xml
        <select id="selectEmp" resultType="emp">
        		select * from employee where id = #{id}
        </select>
        ```

    - 第二种方式：用package批量起别名，都默认为类名的小写

      - name：要批量起名的包

      - ```xml
        <package name="com.hsh.bean" /> 
        ```

    - 第三种方式：在package的方法基础上，用注解 @Alias 单独为某个类指定别名

      - ```xml
        @Alias(emp)
        public class Employee{
        ...
        }
        ```

  - **enviroments 标签**：包含多个配置环境，有default属性

    - default 属性：来确定使用哪个 enviroment；可以是测试和开发时使用不同环境，也可以是使用不同数据库

    - 标签

      - environment 标签 ： 一个环境，有id 属性，还有两个标签
        - ID 属性：用来表示环境
        - 标签
          - transactionManager 标签： 事务管理器，有一个属性type，用来指定使用哪个类型
          - dataSource 标签：数据源，有一个属性type，用来指定数据源类型，还有一个标签property

    - 样例

      - ```xml
        <environments default="development">
            <enviroment type="test">	
                <transactionManager type="JDBC">
                    ...
                </transactionManager>
                <dataSource	type="POOLED">
                    ...
                </dataSource>
            </enviroment>
        
            <environment id="development">
                <transactionManager type="JDBC" />
                <dataSource type="POOLED">
                    <property name="driver" value="${jdbc.driver}" />
                    <property name="url" value="${jdbc.url}" />
                    <property name="username" value="${jdbc.name}" />
                    <property name="password" value="${jdbc.password}" />
                </dataSource>
            </environment>
        </environments>
        ```

      - 

  - **databaseIdProvider 标签**：区分使用不同的数据库厂商，有一个属性type

    - ```xml
      <!--全局配置文件中 -->
      <databaseIdProvider type="DB_VENDOR">
      	<property name"MYSQL" value="mysql" />
          <property name="Oracle" value="oracle" />
      </databaseIdProvider>
      
      <!--切换到Oracle数据库中 -->
      <environments default="Oracle">
          <enviroment type="Oracle">	
              ...
          </enviroment>
      
          <environment id="MySQL">
              ...
          </environment>
      </environments>
      
      ```

    - ```xml
      <!--Mapper文件中 -->
      <!-- 指明这个语句使用的是oracle数据库，而不是mysql-->
      <select id="selectEmp" resultType="emp" database="oracle">
      		select * from employee where id = #{id}
      </select>
      ```

  - **mapper 标签**：注册一个sql映射，有以下三个属性

    - resource 属性：引用类路径下的sql映射文件
    - url 属性：引用网络路径或磁盘路径的sql映射文件
    - class 属性：通过引用接口实现
      - 有sql映射文件，则该映射文件必须和dao层的接口在一个包中，且和接口同名
      - 没有sql映射文件，则需要在dao层的接口中对应方法上利用注解来写sql语句

  - **package 标签** ： 批量注册sql映射，直接添加包名

    - ```xml
      <package name="com.hsh.dao" />
      ```

    - 前提是对应的XML文件和dao层接口在一个文件夹下

# sql映射文件

- 每一个sql映射文件一定要在全局配置文件中配置才能生效

### 增删改查

- ```xml
  <insert id="addEmp">
  	insert into employee(last_name,gender,eamil)
      values(#{last_name},#{gender},#{email})
  </insert>
  <delete>
  	delete from employee
      where id=#{id}
  </delete>
  <update>
  	update employee
      set last_name=#{last_name}
      where id=#{id}
  </update>
  <select>
  	select * from employee where id=#{id}
  </select>
  ```

- ```java
  public interface EmployeeMapper{
      ...
      public Boolean addEmp(Employee employee);		
      //sql映射文件中没有设置返回值resultType，但可以再接口中直接定义需要什么返回值即可，还支持Integer，Long类型
  }
  ```

- ```java
  public void test(){
      ...
      Employee employee = new Employee("jerry","1","jerry@qq.com");
      mapper.insert(employee);
      openSession.commit();		//要手动提交
  }
  ```

### 获取自增主键

- ```xml
  <!--mybatis自动将数据库中插入的对象的id值赋给java代码中创建的对象的id属性 -->
  <insert id="addEmp" useGeneratedKeys="true" keyProperty="id">
  	...
  </insert>
```
  
- ```java
  public void test(){
  	...
      Employee employee = new Employee(null,jerry","1","jerry@qq.com")
      mapper.addEmp(employee);//如果不在对应的插入语句中使用useGeneratedKeys="true" keyProperty="id"，id值为0
      int id = employee.getId();  //可以获取到刚插入数据库中对象的ID值
  }
  ```

### 获取非自增的主键的值

- keyProperty ：查出的键值封装给javabean的那个属性，接下来可能会用

- order="BEFORE" ： 当前sql在插入前先运行

  ​           =“AFTER" ： 当前sql在插入后再运行

- resultType ： 查出的数据的返回值类型，你想要这条查出来的数据封装成什么样子，此处就填什么，比如类，集合等

- ```xml
  <!--应对有的数据库不支持查询自增ID -->
  <insert id="addEmp">
  	<selectKey keyProperty="id" order="BEFORE" resultType="integer">
      	select max(id)+1 from Employee
      </selectKey>
      insert into employee(id,name, gender, email)
      values(#{id},#{name}, #{gender}, #{email})			//此处的id是selectkey标签查询出来的值
  </insert>
  ```

### 参数处理

- 单个参数

  - #{ ...} ： 大括号里面写任何东西都可以，mybatis不会查询验证

- 多个参数

  - 利用Map 传参，sql语句直接从map对象中根据key取相应参数

    - ```xml
      <select id="getEmpByIdAndName" resultType="com.hsh.bean.Employee">
      		select * from employee where id=#{id} and last_name=#{last_name}
      </select>
      ```

    - ```java
      public interface EmployeeMapper{
          public Employee getEmpByIdAndName(Map<String,String> map);
      }
      ```

    - ```java
      public void test(){
          ...
          Map<String,String> map = new HashMap<>();
          map.put("id","1");
          map.put("last_name","tom1" );
          Employee employee = mapper.getEmpByIdAndName(map);
          System.out.println(employee);
          ...
      }
      
      ```

### select 

- 概述
  - select有3个属性 ：id 和 resultType / resultMap 
    - id 属性用来标识这个select语句，它的值就是dao层接口中的方法名
    - resultType：用来直接返回**单个对象**时使用
    - resultMap：对象中还有**其他对象或者集合**时使用

#### 返回值是集合

- 接口中定义的方法返回值是集合形式，sql映射文件中只需要将sql语句的返回值设置为集合中存储的元素即可，mybatis自己封装成集合返回

- ```xml
  <select id="getEmpByIdLike" resultType="com.hsh.bean.Employee">
  	select * from employee where last_name like #{string}  
  </select>
  ```

- ```java
  public interface EmployeeMapper{
      public List<Employee> getEmpByIdLike(String string);
  }
  ```

- ```java
  public void test(){
      ...
      List<Employee> list = mapper.getEmpByIdLike("j%");	//返回姓名字段中所有以j开头的行
      
  }
  ```

#### 返回值是map的情况

- 返回**一条记录**的map， key是列名，value是对应的值

  - ```xml
    <select id="getEmpMapById" resultType="map">	<!--想要这条记录封装成什么就写成什么 -->
    	select * from employee where id=#{id}
    </select>
    ```

- 返回**多条记录**的map，key是主键，value是employee对象

  - 注解@MapKey 和 返回对象是map中值的类型
  
  - ```xml
    <select id="getEmpMapByName" resultType="com.hsh.bean.Employee">	
        <!--想让每一条记录封装成employee对象，所以此处不是map -->
        select * from employee where last_name like #{last_name}
  </select>
    ```
  
  - ```java
    public interface EmployeeMapper{
        @MapKey("id")			//加注解，表示返回的map中主键为对象的ID值
    	public Map<Integer,Employee> getEmpMapByName(String string);
  }
    ```
  
  - ```java
    public void test(){
        ...
        Map<Integer, Employee> empMapById = mapper.getEmpMapByName("%j%");
    }
    ```

#### resultMap 

- 自定义结果集映射规则，将数据库表中列名和javabean的属性名映射上，有两个association 和 collection 两种标签

- 对象中还有另一个对象时如何返回：使用**association**

  - 通过联结的方式查询
  
  - ```java
    public class Employee{
        private int id;
        ...
        private Department dept;		//对象中有另一个类的对象时，用association返回
    }
    
    public class Department{
        private int dep_id;
        private String name;
    }
    ```
  
  - ```xml
    <!--自定义Employee对象的封装规则  -->
    <resultMap type="com.hsh.bean.Employee" id="MyEmp">	
        <!--type属性指明你想要封装的对象、
  		    id标签定义主键，底层有优化、
    		column 属性指定数据库中哪一列
    		property属性指定对应的javaBean属性
    		result标签定义其余列
    		<association> 标签用来定义关联的单个对象的封装规则，此处employee对象中还存储department对象
    			property 属性指定该association代表类中哪个属性
    			Javatype 属性指定该association返回什么javabean对象
    			association的id列中不能和上一级的id重名
    	-->
    		<id column="id" property="id"/>
    		<result column="last_name" property="last_name"/>
    		<result column="gender" property="gender"/>
    		<result column="email" property="email"/>
    		
    		<association property="dept" javaType="com.hsh.bean.Department">
    			<id column="did" property="dep_id"/>
    			<result column="dept_name" property="name"/>
    		</association>
    </resultMap>
    
    <!--resultMap属性的值就是上面resultMap标签的id，查询结果根据自定义规则去封装 -->
    <select id="getEmpInDepById" resultMap="MyEmp">
    		select e.id as id, e.last_name as last_name, e.gender as gender, e.email as email, 
    		d.id as did, d.name as dept_name
    		from employee AS e, department AS d
    		where e.emp_dept = d.id and e.id=#{id}
    		ORDER BY e.id;
    </select>
    ```
  
- 通过分步的方式查询（和上面联结的方法结果一样）

  - 步骤：先从employee表中根据ID查出某个人，然后根据其所属部门ID继续在department表中查询到所属部分并返回给employee对象的dept属性

  - department的部分

  - ```xml
    public interface DepartmentMapper {
    	public Department getDepById(Integer id);
    }
    ```

  - ```xml
    <mapper namespace="com.hsh.dao.DepartmentMapper">
        <select id="getDepById" resultType="com.hsh.bean.Department">
            select * from department where id=#{id}
        </select>
    </mapper>
    ```

  - employee的部分

  - ```xml
    <mapper>
    	<select id="getEmpById" resultMap="stepSelect">
    		select * from employee where id=#{id}
    	</select>
    	<resultMap type="com.hsh.bean.Employee" id="stepSelect" >
    	 	<id column="id" property="id"/>
    	 	<result column="last_name" property="last_name"/>
    	 	<result column="gender" property="gender"/>
    	 	<result column="email" property="email"/>
    	 	
    	 	<association property="dept"
    	 		select="com.hsh.dao.DepartmentMapper.getDepById"
    	 		column="emp_dept">
                <!--property 属性指示association要返回的对象类型,就是javabean中属性名字
    	 			select 属性指示需要执行的Mapper文件中的select语句
    				column 属性指示查询所需参数
    			整个流程是使用select指定的方法，根据column指定的列传入的参数来查询，并封装给property指定的属性
    			-->
    	 	</association>
    	</resultMap>
    </mapper>
    ```
  
- 延迟加载（按需加载）：在全局配置文件中配置

  - lazyLoadingEnabled：true 时，则分步查询时如果不用部门相关信息那么就不会进行第二步的查询，反之，每次会查询所有信息

  - aggressiveLazyLoading： false时，则一个对象的所有延迟加载属性都不会完全加载

  - ```xml
    <setting  name="lazyLoadingEnabled" value="true"/>	
    <setting name="aggressiveLazyLoading" value="false"/>
    ```

- 对象中有一个集合的情况下该如何返回：使用**collection标签**

  - 通过联结的方式查询

  - department

    - ```java
      public class Department{
          private int dep_id;
      	private String name;
      	private List<Employee> emps;		//根据部门ID检索时找出employee表中该部门的员工
          ...
      }
      ```

    - ```java
      public interface DepartmentMapper {
      	public Department getDepById(Integer id);
      	public Department getDepAndEmpById(Integer id);
      }
      ```

    - ```xml
      <select id="getDepAndEmpById" resultMap="myDep">
          select d.id as did, d.name as dept_name, e.id as emp_id, e.last_name as emp_name, e.gender as gender,
          e.email as email
          from department as d, employee as e 
          where d.id=e.emp_dept and d.id=#{id}
      </select>
      <resultMap type="com.hsh.bean.Department" id="myDep">
          <id column="did" property="dep_id"/>
          <result column="dept_name" property="name"/>
          <!-- collection 定义关联集合类型的属性的封装规则
      		ofType 属性指定集合中的元素类型
      	-->
          <collection property="emps" ofType="com.hsh.bean.Employee">
              <id column="emp_id" property="id"/>
              <result column="emp_name" property="last_name"/>
              <result column="gender" property="gender"/>
              <result column="email" property="email"/>
          </collection>
      </resultMap>
      ```
    
- 通过分步方式查询
  
  - department 部分
  
    - ```xml
        <select id="getDepAndEmpByIdStep" resultMap="stepDep">
            select * from department where id=#{id}
        </select>
        <resultMap id="stepDep" type="com.hsh.bean.Department">
            <id column="id" property="dep_id"/>
            <result column="name" property="name"/>
            <collection property="emps"
                        select="com.hsh.dao.EmployeeMapper.selectEmpByDepid"
                        column={deptId=id,...}
                        <!-- 传多个参数时，将多列的值封装成map传递，此处的deptId就是dao层接口需要的参数名 -->
                        fetchType="lazy">	
            			<!-- lazy表示使用延迟加载，eager表示立即加载-->   		
            </collection>
        </resultMap>
        ```
      
  - employee 部分
  
    - ```java
      public interface EmployeeMapper{
            public List<Employee> selectEmpByDepid(Integer deptId);
        }
        ```
    
    - 
  
    - ```xml
      <select id="selectEmpByDepid" resultType="com.hsh.bean.Employee">
            select * from employee where emp_dept=#{deptId}
            <!--根据department表中ID做参数，在employee表中查询 -->
        </select>
        ```
    
  - **鉴别器 discriminator，子标签 case** 

    - 判断某列值，根据这个值做不同操作

    - 属性：

      - javaType ： 指示这个字段是什么类型
    - column：这个列的列名
  
    - 子标签 case ， 属性如下

      - value ：列值
    - resultType ：要返回的对象类型
  
    - ```xml
    <resultMap type="com.hsh.bean.Employee" id="myEmpDept">
          <id column="id" property="id"/>
          <result column="last_name" property="last_name"/>
          <result column="gender" property="gender"/>
          <result column="email" property="email"/>
          
      	<!-- 若gender为0，只获取所在部门的id，gender为1，获取部门ID和名字-->
          <discriminator javaType="String" column="gender">
              <case value="0" resultType="com.hsh.bean.Department">
                  <association property="dept" javaType="com.hsh.bean.Department">
                      <id column="did" property="dep_id"/>
                  </association>
              </case>
              <case value="1" resultType="com.hsh.bean.Department">
                  <association property="dept" javaType="com.hsh.bean.Department">
                      <id column="did" property="dep_id"/>
                      <result column="dept_name" property="name"/>
                  </association>
              </case>
          </discriminator>
      </resultMap>
      ```

## #｛｝和$｛｝的区别

#{属性名}：使用参数预编译方式填充；在控制台打印时使用 ？替换参数，参数是后来预编译设置才放进去的；好处是比较安全

${属性名}：不使用参数预编译方式填充；直接将括号内容填充到语句中，而sql语句只有参数位置支持预编译方式填充；缺点是不安全；优点是可以使用在sql语句特定位置。

```my
使用${属性名} 填充到from后。若使用#{属性名}方式sql语句不能执行
select id,name from ${...} where ...
```



# 动态SQL

- 概述

  - if ( test )
  - choose ( when, otherwise ) ： switch
  - trim (where, set)
  - foreach
  - bind
  - sql语句重用

- **if**

  - ```xml
    <select>
    	select ... from ...
        where
        <where>	 
         	<if test="id!=null and last_name !=null ">			<!--当ID和name都不为空时，就带上这个字段 -->
        		id=#{id}, last_name=#{last_name}
        	</if>
        	<if test="gender != null">
        		and gender =#{gender}
        	</if>
            ...
        </where>		
    </select>
    ```

  - test 表达式的规范参考 ognl 规范，test的等号右边会自动转格式，不用写双引号

  - where 标签

    - 加上where标签后，即使第一个if为false，那么sql语句也能正常拼接，where会自动去掉第二个if的and
    - 若不用where标签，那么就在where子句后面加上 ”1=1“，后面接上if标签

  - trim 标签解决where标签不能解决的问题

    - trim 标签 包裹整个where子句，它有四个属性

      - prefix：前缀，给整个字符串添加一个前缀
      - prefixOverride：前缀覆盖，去掉整个字符串前面多余的字符串
      - suffix：后缀，给整个字符串添加一个后缀
      - suffixOverride：后缀覆盖，去掉整个字符串后面多余的字符串

    - ```xml
      select ... from ...
      <trim prefix="where" suffixOverride="and">
      	<if test="id!=null and last_name !=null ">			
          	id=#{id}, last_name=#{last_name} and
          </if>
          <if test="gender != null">
          	gender =#{gender} and
          </if>
           ...
      </trim>
      <!--and 都放在每个if子句的后面，用prefix和suffixOverride来拼接出整个where子句 -->
      ```

    - 解决set的逗号问题

    - ```xml
      update ... 
      <trim prefix="set" suffixOverride=",">
      	<if test="id!=null">
          	id=#{id},
          </if>
          <if test="gender!=null">
          	gender=#{gender},
          </if>
          ...
      </trim>
      ```

- **choose** ( when, otherwise ) 

  - ```xml
    select ... from ...
    <where>
    	<when test="id!=null">
        	id=#{id}
        </when>
        <when test="last_name!=null">
        	last_name=#{last_name}
        </when>
        ...
    </where>
    ```

  - 功能相当于switch，那个符合就执行那个，自动break

- **foreach** 

  - 属性

    - collection：指定要遍历的集合
      - 它的属性值可以是 list 或 array 或map，根据传来的参数确定
    - item：将当前遍历出的元素赋值给 item 指定的变量
    - separator：每个元素之间的分隔符
    - open：遍历出所有结果拼接一个开始的字符
    - close：遍历出所有结果拼接一个结束的字符
    - index：索引。
      - 遍历list的时候index就是索引，Item就是当前值。
      - 遍历map是index就是key，Item就是value
    - #{变量名} 就能取出变量的值，也就是当前正在遍历的元素

  - 演示

    - ```xml
      <select>
      	<!-- select * from employee where id in (1,2,3,4) 用foreach实现 -->
          select * from employee
          <foreach collection="list" item="item" separator="," open="where id in (" close=")" >
         	 	#{item}
          </foreach>
      </select>
      ```

  - 批量插入数据

    - ```xml
      <insert id="addEmpForeach">
          insert into employee(last_name,gender,email,emp_dept)
          values
          <foreach collection="list" item="item" separator="," >
              (#{item.last_name}, #{item.gender}, #{item.email}, #{item.dept.dep_id}) 		
          </foreach>
      </insert>
      ```

    - ```java
      ...
      List<Employee> list = new ArrayList<>();
      list.add(new Employee(null,"smith","1","smith@qq.com", new Department(4,"后勤部")));
      list.add(new Employee(null,"smith1","0","smith1@qq.com", new Department(5,"营销部")));
      mapper.addEmpForeach(list);
      openSession.commit();
      ```

- **bind 标签**

  - 将OGNL表达式的值绑定到一个变量中，方便以后引用这个变量的值

  - 演示

    - ```xml
      <!--传一个employee，他的名字为e，找出所有名字含有e的employee对象 -->
      <select id="..." resultType="com.hsh.bean.Employee">
      	<!-- value的值像字符串拼接一样实现，将value赋给name的属性值，语句中用它代替原有参数 -->
          <!-- last_name时employee对象的属性名 -->
          <bind name="_name" value="'%'+last_name+'%'"/>
          select * from employee where last_name like #{_name}
      </select>
      ```

- sql语句重用：sql 标签 中也可以自己做判断 ； include 标签来引用

  - ```xml
    <sql id="cols">
        <if test="_databaseId=='mysql'">
         	id,last_name,gender, ${testColumn}
        </if>
        <if test="_databased=='oracle'">
        	ora_id,ora_name,ora_gender
        </if>
    </sql>
    
    <select>
    	select 
        <include refid="cols"/></include>
    	from employee where id=#{id}
    </select>
    ```

  - sql 语句中如果要自定义属性，需要用 ${} 而不是 #{} 

# 缓存

- 概述
  - 虽然mybatis有缓存机制，但不够专业，所以需要添加其他缓存插件，如redis等
  - **一级缓存**：
    - 本地缓存，默认开启
    - **基于sqlSession级别的缓存**，一个sqlSession对应一个缓存区
    - 与数据库同一次回话期间查询到的数据会放在本地缓存中，以后需要获取相同的数据，直接从缓存中拿，不需要再去查询数据库
  - **二级缓存**：
    - 全局缓存，手动开启
    - **基于namespace级别的缓存**，一个namespace对应一个二级缓存，满足不同sqlSession提取进行同一个查询的情况
      - 比如查询department的表，因为部门很少改变，所以当其他sqlSession需要查询部门时便可以直接从二级缓存中查询
    - 工作机制
      - 一个会话，查询一条数据，这个数据就会被放在当前会话的一级缓存中
      - 如果这个sqlSession不关闭，就不会迁移到二级缓存，如果这个会话关闭，那么一级缓存的内容以所在namespace为标识，被保存到二级缓存中，若新的会话查询相同内容，就可以直接在二级缓存中提取
      - 不同的namespace查出的数据会放在自己对应的缓存中（被封装成map）
- 和缓存有关的设置和属性
  - 全局配置文件中 cacheEnabled=true / false 打开/ 关闭二级缓存，一级缓存一直可以使用
  - 每个select标签中有 useCache属性，为true 该标签使用二级缓存，为false不使用二级缓存，一级缓存默认使用
  - 每个增删改标签中有 flushCache="true" 表示 一，二级缓存清空，操作执行完就会清空缓存
  - sqlSession.clearCache() 只是清除当前session的一级缓存
  - localCacheScope 本地缓存作用域，属于一级缓存

### 一级缓存

- 一级缓存失效的情况
  - sqlSession不同
  - sqlSession相同，但查询的不是同一个对象
  - sqlSession相同，但中间有一个增删改操作
  - sqlSession相同，但手动清除缓存区

### 二级缓存

- 使用

  - 全局配置文件中

    - ```xml
      <setting name="cacheEnabled" value="true" />
      ```

  - sql映射文件中

    - ```xml
      <mapper>
      	<cache/></cache>      <!--只写cache标签也可以，还可以定义属性 -->
      </mapper>
      ```

  - cache标签的属性

    - eviction：缓存回收策略，默认最近最少使用算法LRU
    - flushInterval：缓存刷新间隔
    - readOnly：是否只读，如果只读那么mybatis会将数据在缓存中的引用交给用户，如果不是，那么mybatis利用反序列化技术，将原来的数据克隆一份交给用户
    - size：缓存存放多少元素
    - type：指定自定义缓存的全类名，这个类需要实现接口Cache
    - 

# spring和mybatis整合

# mybatis generator 逆向工程

- 概述
  - 一般开发过程需要先手动创建一个数据库表，然后创建对应的javabean类，写接口，写xml。mybatis的逆向工程可以通过数据库表推断出bean类，接口，xml文件，不需要自己亲自操作

# mybatis 底层原理

# 了解原理后写插件



## 补充

- #{..} 和 ${..} 的区别

  - ```xml
    <select>
    	select * from employee where id={id} and last_name=#{last_name}
        <!--获取参数后sql语句为： select * from employee where id=id and last_name=? -->
    </select>
    ```

  - #{..} ：以预编译的形式，将参数设置到sql语句中，一般用于sql语句的参数部分

  - ${..} ：去除的值直接拼装在sql语句中，会有安全问题

  - 一般用 #{..}，但原生jdbc不支持使用占位符的地方就需要使用 ${..} 。比如 表名或排序的地方

    - ```mysql
      select * from ${table} where xxx; 
      select * from employee order by ${id} ${order}
      ```

- 在全局配置文件中，数据库的配置信息处使用 " ${jdbc.driver}" 来获取参数

  在sql映射文件中，sql语句中使用 " #{id} " 来获取参数