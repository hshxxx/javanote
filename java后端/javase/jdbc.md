# JDBC 概述

## 介绍

1. JDBC 全称 Java Database Connective 是一个独立于某个特定数据库管理系统，通用的SQL数据库存取和操作的公共接口（java.sql 和 javax.sql）。
2. JDBC为访问不同数据库提供了统一的途径。
3. JDBC 的目标是可以让程序员操作任何**提供了JDBC驱动程序的数据库系统**，提高开发效率。

## JDBC 程序编写步骤

statement 以被替换为 preparedStatement

![image-20201115131544248](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201115131544248.png)

# 获取数据库连接

## Driver接口

1. 介绍
   - Java.sql.Driver 接口是所有JDBC 驱动程序需要实现的接口。这个接口是提供给数据库厂商使用的，不同厂商提供不同的实现。
   - 程序中不需要直接访问实现了Driver 接口的类，而是由驱动程序管理类去调用这些Driver实现
     - mysql 的驱动：com.mysql.jdbc.Driver



## 获取数据库连接

**使用方法4**

1. 方式一

```java
 public void getMysqlConnection1() throws SQLException {
        //不同的数据库厂商实现不同的Driver
        Driver driver = new com.mysql.jdbc.Driver();
        //url：数据库的连接地址
        //jdbc:mysql：数据库协议
        //localhost：IP地址
        //3306：端口号
        //test：数据库名
        String url = "jdbc:mysql://localhost:3306/test";
        //properties 文件存储用户名和密码
        Properties properties = new Properties();
        properties.setProperty("user","root");
        properties.setProperty("password","123456");
        //根据驱动器和properties文件内容获取数据库的连接
        Connection connect = driver.connect(url, properties);
        System.out.println(connect);

    }
```

2. 方式2

   DriverManager 是 Driver接口的实现类，它管理一系列的数据库驱动

   ```java
   public void getMysqlConnection2() throws Exception {
           //利用反射获取MySQL数据库的驱动
           Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");
           Driver driver = (Driver)aClass.newInstance();
           //定义数据库地址，用户名，密码
           String url = "jdbc:mysql://localhost:3306/test";
           String user = "root";
           String password = "123456";
           //将驱动注册到DriverManager中，并从中获取
           DriverManager.registerDriver(driver);
           Connection connection = DriverManager.getConnection(url, user, password);
           System.out.println(connection);
   
       }
   ```

3. 方式3

   ```java
   public void getMysqlConnection3() throws Exception {
           //定义数据库地址，用户名，密码
           String url = "jdbc:mysql://localhost:3306/test";
           String user = "root";
           String password = "123456";
            //利用反射获取MySQL数据库的驱动
           Class<?> aClass = Class.forName("com.mysql.jdbc.Driver");
       	//这两步可以省略，DriverManager自动帮我们注册驱动
           //Driver driver = (Driver)aClass.newInstance();
           //DriverManager.registerDriver(driver);
           Connection connection = DriverManager.getConnection(url, user, password);
           System.out.println(connection);
   
       }
   ```

4. **方式4**

   优点：实现数据与代码的分离。如果需要修改配置信息，不用重新打包程序。

   ```java
    public void getMysqlConnection3() throws Exception {
           //获取定义的jdbc.properties文件
           InputStream resourceAsStream = test1.class.getClassLoader().getResourceAsStream("jdbc.properties");
           //加载定义的jdbc.properties文件
           Properties properties = new Properties();
           properties.load(resourceAsStream);
           //获取jdbc.properties文件中的键值对
           String user = properties.getProperty("user");
           String password = properties.getProperty("password");
           String url = properties.getProperty("url");
           String driver = properties.getProperty("driver");
           //加载驱动，获取连接
           Class.forName(driver);
           Connection connection = DriverManager.getConnection(url, user, password);
           System.out.println(connection);
       }
   ```

   ```properties
   user=root
   password=123456
   url=jdbc:mysql://localhost:3306/test
   driver=com.mysql.jdbc.Driver
   ```

   

# PreparedStatement实现CRUD

增删改 用一个方法实现，查用另一个方法实现

1. 实现insert

   ```java
   public void insertTest() throws Exception {
           //获得数据库连接
           InputStream resourceAsStream = ClassLoader.getSystemClassLoader().getResourceAsStream("jdbc.properties");
           Properties properties = new Properties();
           properties.load(resourceAsStream);
           String user = properties.getProperty("user");
           String password = properties.getProperty("password");
           String url = properties.getProperty("url");
           String driver = properties.getProperty("driver");
           Class.forName(driver);
           Connection connection = DriverManager.getConnection(url, user, password);
           //编写sql语句，将其交给connection的preparedStatement去带参执行
           String sql = "insert into customers(name,email,birth)values(?,?,?)";
           PreparedStatement preparedStatement = connection.prepareStatement(sql);
       	//设置占位符
           preparedStatement.setString(1,"zhangsan");
           preparedStatement.setString(2,"zhangsan@qq.com");
           SimpleDateFormat simpleDateFormat = new SimpleDateFormat("yyyy-MM-dd");
           java.util.Date parse = simpleDateFormat.parse("2020-11-15");
           //util包下的data不能用于数据库中的日期对象
           preparedStatement.setDate(3,new java.sql.Date(parse.getTime()));
           //执行preparedStatement
           preparedStatement.execute();
           //关闭资源
           preparedStatement.close();
           connection.close();
   
       }
   ```

2. jdbcUtils工具类

   ```java
   public class JdbcUtils {
       public static Connection getConnection() throws Exception {
           InputStream resourceAsStream = ClassLoader.getSystemClassLoader().getResourceAsStream("jdbc.properties");
           Properties properties = new Properties();
           properties.load(resourceAsStream);
           String user = properties.getProperty("user");
           String password = properties.getProperty("password");
           String url = properties.getProperty("url");
           String driver = properties.getProperty("driver");
           Class.forName(driver);
           Connection connection = DriverManager.getConnection(url, user, password);
           return connection;
       }
   
       public static void closeConnection(Connection connection, Statement statement) throws Exception {
           if(statement != null){
               statement.close();
           }
           if (connection != null){
               connection.close();
           }
       }
   }
   ```

   

3. 通用的增删改方法update

   增加，删除，修改的sql语句都可以使用update方法

   ```java
   public void update(String sql,Object ... args) throws Exception {
           Connection connection = JdbcUtils.getConnection();
           PreparedStatement preparedStatement = connection.prepareStatement(sql);
           for (int i=0; i<args.length;i++){
               preparedStatement.setObject(i+1,args[i]);
           }
           preparedStatement.execute();
           JdbcUtils.closeConnection(connection,preparedStatement);
       }
   ```

4. 通用的查找返回单行数据的select

   ```java
   public static  <T> T queryForOne(Class<T> type, String sql, Object ... args) throws Exception {
       //首先获取connection和preparedStatement对象
       Connection connection = JdbcUtils.getConnection();
       PreparedStatement preparedStatement = connection.prepareStatement(sql);
       //填充占位符
       for (int i=0; i<args.length;i++){
           preparedStatement.setObject(i+1,args[i]);
       }
       //执行preparedStatement，用的是 executeQuery方法，返回结果集
       ResultSet resultSet = preparedStatement.executeQuery();
       //获取元数据
       ResultSetMetaData metaData = resultSet.getMetaData();
       //获取数据库表的列的数量
       int columnCount = metaData.getColumnCount();
       //判断是否查找到结果，若找到结果，遍历每一行
       if(resultSet.next()){
           //创建一个参数的对象
           T t = type.newInstance();
           //遍历每一列
           for (int i=0; i<columnCount;i++){
               //获取列名和列的值
               String columnLabel = metaData.getColumnLabel(i + 1);
               Object columnValue = resultSet.getObject(i + 1);
               //利用反射将值插入到刚创建的对象中
               Field declaredField = type.getDeclaredField(columnLabel);
               declaredField.setAccessible(true);
               declaredField.set(t,columnValue);
           }
           //关闭连接，返回结果
           JdbcUtils.closeConnectionAndResultset(connection,preparedStatement,resultSet);
           return t;
       }
       return null;
   }
   ```

5. 通用的返回多行数据的select

   ```java
   public static <T> List<T> queryForList(Class<T> type, String sql, Object... args) throws Exception {
       //首先获取connection和preparedStatement对象
       Connection connection = JdbcUtils.getConnection();
       PreparedStatement preparedStatement = connection.prepareStatement(sql);
       //填充占位符
       for (int i = 0; i < args.length; i++) {
           preparedStatement.setObject(i + 1, args[i]);
       }
       //执行preparedStatement，用的是 executeQuery方法，返回结果集
       ResultSet resultSet = preparedStatement.executeQuery();
       //获取元数据
       ResultSetMetaData metaData = resultSet.getMetaData();
       //获取数据库表的列的数量
       int columnCount = metaData.getColumnCount();
       //查询结果
       List<T> list = new ArrayList<T>();
       //判断是否查找到结果，若找到结果，遍历每一行
       while (resultSet.next()) {
           //创建一个参数的对象
           T t = type.newInstance();
           //遍历每一列
           for (int i = 0; i < columnCount; i++) {
               //获取列名和列的值
               String columnLabel = metaData.getColumnLabel(i + 1);
               Object columnValue = resultSet.getObject(i + 1);
               //利用反射将值插入到刚创建的对象中
               Field declaredField = type.getDeclaredField(columnLabel);
               declaredField.setAccessible(true);
               declaredField.set(t, columnValue);
           }
           list.add(t);
       }
       //关闭连接，返回结果
       JdbcUtils.closeConnectionAndResultset(connection, preparedStatement, resultSet);
       return list;
   }
   ```

   

## 小结

1. 面向接口编程：driver接口留给厂商，厂商根据自己的数据库做开发，而java开发人员只需要面向JDBC编程即可。
2. ORM思想：object relational mapping 
   - 一个数据表对应一个java类
   - 一行数据代表一个java对象
   - 一列数据代表一个java对象的属性

# Blob类型

MySQL中，blob 是一个二进制大型对象，插入blob类型的数据必须使用PreparedStatement，因为BLOB类型的数据无法使用字符串拼写的。

MySQL中四种BLOB类型（只在存储能力上又分别）

| 类型       | 最大大小，单位字节 |
| ---------- | ------------------ |
| TinyBlob   | 255                |
| Blob       | 65k                |
| MediumBlob | 16M                |
| LongBlob   | 4G                 |

实例：

1. 向数据库中插入Blob类型

```java
public  void insertBlob() throws Exception {
        Connection connection = JdbcUtils.getConnection();
        String sql = "insert into customers(name,email,birth,photo)values(?,?,?,?)";
        PreparedStatement preparedStatement = connection.prepareStatement(sql);
        preparedStatement.setObject(1,"trump");
        preparedStatement.setObject(2,"trump@qq.com");
        preparedStatement.setObject(3,"1956-10-1");
    //获取文件输入流
        FileInputStream fileInputStream = new FileInputStream(new File("1.jpg"));
    //用setBlob方法设置
        preparedStatement.setBlob(4,fileInputStream);
        preparedStatement.execute();
        JdbcUtils.closeConnection(connection,preparedStatement);
    }
```

2. 从数据库中获取Blob类型

```java
public void queryBlob() throws Exception {
    String sql = "select photo from customers where id = ?";
    Connection connection = JdbcUtils.getConnection();
    PreparedStatement preparedStatement = connection.prepareStatement(sql);
    preparedStatement.setObject(1,20);
    ResultSet resultSet = preparedStatement.executeQuery();
    if(resultSet.next()){
        Blob photo = resultSet.getBlob("photo");
        InputStream binaryStream = photo.getBinaryStream();
        FileOutputStream zhaopian = new FileOutputStream("zhaopian.jpg");
        byte[] buffer = new byte[1024];
        int len;
        while((len =binaryStream.read(buffer)) != -1 ){
            zhaopian.write(buffer,0,len);
        }

        JdbcUtils.closeConnectionAndResultset(connection,preparedStatement,resultSet);

    }
}
```

# 批量插入数据



```java
public static void insertBatch(String sql, int count, Object... args) throws Exception {
    Connection connection = JdbcUtils.getConnection();
    PreparedStatement preparedStatement = connection.prepareStatement(sql);
    for (int i = 0; i < args.length; i++) {
        preparedStatement.setObject(i + 1, args[i]);
    }
    for (int i = 0; i < count; i++) {
        preparedStatement.addBatch();
    }
    preparedStatement.executeBatch();
    preparedStatement.clearBatch();
    JdbcUtils.closeConnection(connection, preparedStatement);
}
```



# 事务

## 事务的ACID属性

1. 原子性（Atomicity）：事务是一个不可分割的工作单位，事务中的操作要么都发生，要么都不发生。
2. 一致性（Consistency）：事务必须使数据库从一个一致性状态变换到另一个一致性状态。
3. 隔离性（Isolation）：一个事务的执行不能被其他事务干扰，即一个事物内部的操作及使用的数据对并发的其他事务是隔离的，并发执行的各个事务之间不能互相干扰。
4. 持久性（Durability）：一个事务一旦被提交，他对数据库中数据的改变是永久的，接下来的其他操作和数据库故障不能对其有任何影响。

# 数据库连接池

JDBC的数据库连接池使用javax.sql.DataSource 来表示，DataSource是一个接口。

DataSource 通畅被称为数据源，它包含连接池和连接池管理两个部分，习惯上成为连接池。

DataSource用来取代DriverManager来获取Connection

1. 不用数据库连接池的缺点
   - 用JDBC操作数据库时，每一次对数据库的操作都要建立一个连接，执行语句然后断开连接。当多个用户连接数据库时服务器会崩溃。
   - 每个用户的一个业务处理可能需要多次操作数据库，而每次都是单独的连接，那么数据库连接没有被充分利用。
   - 直接使用jdbc操作数据库不能控制被创建的连接对象数，系统会无限制创建数据库连接，最后导致崩溃。

2. 数据库连接池的优点

   - 数据库连接池中存储**一定数量**的连接，需要使用链接时，从池子中取出一个，用完了又放回去，连接可以**重复使用**。

     

     

## Druid 

阿里旗下的一个数据库连接池。

## jdbcUtils

```java
public class JdbcUtils {
    private static DruidDataSource dataSource;
    private static ThreadLocal<Connection> threadLocal = new ThreadLocal<>();

    static {
        try {
            Properties properties = new Properties();
            //读取jdbc.properties文件
            properties.load(JdbcUtils.class.getClassLoader().getResourceAsStream("jdbc.properties"));
            //创建 数据库连接池
            dataSource = (DruidDataSource) DruidDataSourceFactory.createDataSource(properties);
           // System.out.println(dataSource);
        } catch (Exception e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {

    }

    /**
     * 获取数据库中的连接
     * @return
     */
    public static Connection getConnection(){
        Connection connection = threadLocal.get();
        if(connection == null){
            try {
                connection=dataSource.getConnection();
                //设置数据库连接为手动管理事务
                connection.setAutoCommit(false);
                //创建好数据库连接后将该连接放入到threadlocal中
                threadLocal.set(connection);
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            }
        }
        return connection;
    }

    /**
     * 提交事务并关闭连接
     */
    public static void commitAndClose() {
        Connection connection = threadLocal.get();
        if(connection != null){ //如果connection不为null，说明以前使用过连接，操作过数据库
            try {
                connection.commit();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            } finally {
                try {
                    connection.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }
        }
        //关闭数据库连接后一定要删除threadlocal中的值，否则出错。因为Tomcat服务器底层使用了线程池技术。
        threadLocal.remove();
    }

    /**
     * 提交事务，并关闭连接
     */
    public static void rollbackAndClose(){
        Connection connection = threadLocal.get();
        if(connection != null){ //如果connection不为null，说明以前使用过连接，操作过数据库
            try {
                connection.rollback();
            } catch (SQLException throwables) {
                throwables.printStackTrace();
            } finally {
                try {
                    connection.close();
                } catch (SQLException throwables) {
                    throwables.printStackTrace();
                }
            }

        }
        //关闭数据库连接后一定要删除threadlocal中的值，否则出错。因为Tomcat服务器底层使用了线程池技术。
        threadLocal.remove();


    }
}
```



































