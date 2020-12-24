# MySQL的使用

## 一. 数据库的连接

- 用户，连接，数据库的关系

  - 不同的用户对应着不同的连接，不同的连接有不同的数据库，所以连接的时候首先登陆用户，再选择连接，然后可以选择数据库进行操作

  - 登录用户

    - ```mysql
      C:\Users\xpty>mysql -u root -p
      Enter password: 123456
      Welcome to the MySQL monitor.  Commands end with ; or \g.
      ```

  - 查看所有数据库：当你不知道连接上都有什么数据库时

    - ```mysql
      mysql> show databases;			//进入数据库后，所有语句要用”;“结尾
      +--------------------+
      | Database           |
      +--------------------+
      | information_schema |
      | db_base_project    |
      | db_online_exam     |
      | mysql              |
      | performance_schema |
      | sys                |
      +--------------------+
      ```

  - 使用数据库

    - ```mysql
      mysql> USE mysql;
      Database changed
      ```

  - 查看数据库中所有的表

    - ```mysql
      mysql> SHOW TABLES;
      ...
      ```

    - 其他show语句

      - show status;   显示广泛的服务器状态信息
      - show create database / table ; 显示创建特定数据库或表
      - show grants; 显示授予用户的权限
      - show errors / warnings 显示服务器错误或警告信息
      - help show； 显示允许的show语句

  - MySQL的退出

    - ```mysql
      mysql> exit;		//用quit也可以
      Bye
      ```

## 二. SELECT的使用



### 1.检索数据 select

- 检索列

  - ```mysql
    SELECT 列名，列名 FROM 表名;	//按列名的顺序检索表中某些列
    SELECT * FROM 表名;  			//检索表中所有的列
    ```

- 检索行

  - ```mysql
    SELECT DISTINCT 列名，列名 FROM 表名;		//去除重复的行，应用于所有列，不能部分使用
    ```

- limit 限制结果

  - ```mysql
    SELECT 列名 FROM 表名 LIMIT 5;		//显示该列的前5行
    SELECT 列名 FROM 表名 LIMIT 5,5;	//显示从该列第5行后的5行
    SELECT 列名 FROM 表名 LIMIT 10,5;	//超出列所含项，只显示能显示的
    ```

- 完全限定的表名

  - 表名.列名  
  - 数据库名.表名

- 关键字
  - select
  - distinct
  - limit

###  2.排序检索数据 order by

- 按升序排序
  ```MYSQL
  SELECT 列1，列2，列3
  FROM 表				  //字母按自然顺序，数字默认按从小到大排序
  ORDER BY 列2，列3；		//先按列2排序，列2相同的情况下按列3排序
  ```

- 按降序排序

  ```mysql
  ORDER BY 列1 DESC, 列2;   //优先按列1的降序排列，当列1相同时按列2的升序排列
  ```

- DESC 和 LIMIT

  ```mysql
  SELECT 列1，列2，列3
  FROM 表				 
  ORDER BY 列2，列3；		
  LIMIT 5;			//LIMIT 放在后面
  ```

- 关键字

  - order by
  - desc ：降序，asc ：升序 ，这两个都只对前面的列有用
  - limit ：放在ORDER BY 后

### 3. 过滤数据 where

- WHERE 子句操作符

  - | =       | 等于               |
    | ------- | ------------------ |
    | <>      | 不等于             |
    | !=      | 和上面的用法一样   |
    | <       |                    |
    | <=      |                    |
    | >       |                    |
    | >=      |                    |
    | BETWEEN | 在指定的两个值之间 |

- ```mysql
  SELECT vend_id, prod_name
  FROM prodects
  WHERE vend_id <> 1003  //给出不是供应商1003制造的所有商品
  ORDER BY vend_id;		//order by 在where之后
  ```

- ```mysql
  SELECT prod_name，prod_price
  FROM prodects
  WHERE prod_name != 'fuses'  //给出所有不叫fuses的商品; 不分大小写，表中是Fuses
  或
  WHERE prod_price BETWEEN 5 AND 10; //给出价格在[5,10]中间的值，包含5和10
  ```

- NULL检查

  - ```mysql
    ...
    WHERE 列 IS NULL;  //给出该列中为空的行
    ```

- 关键字

  - where 
  - order by 在where之后

### 4. where 和 or, and, in, not

- ```mysql
  ...
  WHERE 列1 = 12 AND 列2 <= 33;		
  WHERE 列1 = 12 OR 列2 <= 33;
  WHERE 列1 = 12 OR 列2 <= 33 AND 列3 != 'd';	//and 和 or 的拼接使用，优先处理AND
  WHERE 列1 IN (12,54);  						//在指定范围内 
  WHERE 列1 NOT IN (12,54);					//不再指定范围内
  ```

  - not 可以和 in , between , exists 一起使用

### 5. where 和 like，通配符 % ，_

- % ： 表示**任何字符**出现**任意次数**，任何字符包括**空格**，任意次数包含**0次**，

  - ```mysql
    ...
    WHERE 列 LIKE 'ang%';   //搜索出列中所有以ang开头的文本所在的行，如angel sam
    WHERE 列 LIKE '%ang%';  //搜索出列中所有包含ang的文本所在的行，如 tom angels
    WHERE 列 LIKE 'a%g';		//搜索出列中所有以a开头，g结尾的文本所在的行
    ```

  - % 不能匹配 Null

- _  : 相比 % ，它只匹配**单个字符**而不是多个字符

  - ```mysql
    ...
    WHERE 列 LIKE '_ ton';   //结果可能是1 ton 但不可能是.5 ton，因为.5是两个字符
    ```

  

  

  

### 6. 正则表达式搜索 regexp，. , or, [] , ^

- regexp 和 like 的区别

  - regexp ：匹配的是列中**包含某个值**的行
  - like ：除非使用通配符（%，_），否则只能匹配列中**和某个值相等**的行

- binary：regexp 默认不区分大小写，也可以在regexp之后加**binary**来区分大小写

- . 匹配：. 表示匹配任意一个字符

  - ```mysql
    ...
    WHERE 列 REGEXP '.00';  //匹配出以任意字符开头的然后跟着00的值，如100,200
    WHERE 列 REGEXP '.';  //匹配出列中所有的行，功能类似于 *
    ```

- or匹配

  - ```mysql
    SELECT 列
    FROM 表
    WHERE 列 REGEXP '100|200';	//找出列中包含100或200的行
    ```

- 匹配几个字符之一： [ ] 和 ^

  - ```mysql
    ...
    WHERE 列 REGEXP '[123] ton';		//等于匹配包含 '1 ton'或'2 ton'或'3 ton'的行
    WHERE 列 REGEXP '[1-3] ton';		//简化括号内的书写
    WHERE 列 REGEXP '[a-z] ton';		//也可以匹配字符
    WHERE 列 REGEXP '[^123] ton';	//匹配除'[123] ton'以外的行
    WHERE 列 REGEXP '1|2|3 ton';		//匹配包含1或2或3或ton的行，与用括号不一样
    
    ```

- 匹配特殊字符：用  \\\ 为前导

  - 特殊字符包括  '-',     '.' ,    '|' ,    '[' ,    ']',  '(' ,    ')'

  - 空白元字符

    - | \\\\f | 换页     |
      | ----- | -------- |
      | \\\n  | 换行     |
      | \\\r  | 回车     |
      | \\\t  | 制表     |
      | \\\v  | 纵向制表 |
      | \\\\\ | \ 本身   |

- 匹配字符类：都是提前预定义过的

  - | [:alnum:]  | 任意字母和数字，同[a-zA-Z0-9]                        |
    | ---------- | ---------------------------------------------------- |
    | [:alpha:]  | 任意字符，同[a-zA-Z]                                 |
    | [:blank:]  | 空格和制表，同[\\\t]                                 |
    | [:cntrl:]  | ASCLL控制字符，0到31和127                            |
    | [:digit:]  | 任意数字，同[0-9]                                    |
    | [:graph:]  | 与[:print:]相同，但不包括空格                        |
    | [:lower:]  | 任意小写字母，同[a-z]                                |
    | [:print:]  | 任意可打印字符                                       |
    | [:punct:]  | 既不在[:alnum:]又不在[:cntrl:]中的任意字符           |
    | [:space:]  | 包括空格在内的任意空白字符，同[\\\f\\\n\\\r\\\t\\\v] |
    | [:upper:]  | 任意大写字母，同[A-Z]                                |
    | [:xdigit:] | 任意十六进制数字，同[a-fA-F0-9]                      |
    

- 匹配多个实例

  - 重复元字符：这些元字符对它前面的字符有用

    - | *       | 0个或多个匹配         |
      | ------- | --------------------- |
      | +       | 1个或多个匹配         |
      | ？      | 0个或1个匹配          |
      | ｛n}    | 指定数目的匹配        |
      | ｛n,}   | 不少于指定数目的匹配  |
      | ｛n, m} | 匹配数目的范围，m<255 |

    - ```mysql
      ...
      WHERE 列 REGEXP '\\([0-9] sticks?\\)';  //匹配列中包含用（）括起来的，[0-9] stick 或[0-9] sticks 的行。 ? 表示它前面的s可以有也可以没有
      ```

    - ```mysql
      ...
      WHERE 列 REGEXP '[[:digit:]]{4}'; //匹配列中包含4位整数的行，{4}表示它前面的字符出现4次
      WHERE 列 REGEXP '[[:digit:]]{4,}'; //匹配列中包含不少于4位整数的行
      WHERE 列 REGEXP '[[:digit:]]{4,6}'; //匹配列中包含不少于4位不大于6位整数的行 
      ```

- 定位符：匹配特定位置含有文本的字符串

  - | ^       | 文本的开始 |
    | ------- | ---------- |
    | $       | 文本的结尾 |
    | [[:<:]] | 词的开始   |
    | [[:>:]] | 词的结尾   |

  - ```mysql
    ...
    where 列 regexp '^[0-9\\.]';  //匹配列中以0-9或.开头的行
    where 列 regexp 'vil$';		//匹配列中以vil结尾的行
    ```


### 7. 创建字段和列的别名 concat, as, rtrim, ltrim, trim

- ```mysql
  SELECT Concat(RTrim(vend_name), ' (', vend_country, ')') AS vend_title
  FROM vendors
  ORDER BY vend_name;
  
  创建了一个表，表名为vend_title，表中的项如下
  	ACME (USA)
  	ANVILS R US (USA)
  将供应商的名字和供应商所在国家拼接在一起，将这个新的列命名为vend_title
  ```

- concat(...) ：将括号内各部分拼接起来，每一项用逗号隔开，字符用单引号括起来，单引号内可以有多个字符

- RTrim(...) ：将括号内字符的右边空格消除，LTrim同理，Trim是将两边的空格都消除。 

- AS：给拼接后的字段起一个别名

- ```mysql
  SELECT prod_id, quantity, item_price, quantity*item_prive AS expanded_price
  FROM orderitems
  WHERE order_num = 20005;	//把订单号为2005的订单中所有商品罗列出来，并计算他们的价钱
  ```

### 8. 函数

- 文本处理函数

- | Left()      | 返回串左边的字符    |
  | ----------- | ------------------- |
  | Right()     | 返回串右边的字符    |
  | Locate()    | 找出串中的一个子串  |
  | Lower()     | 将串转换为小写      |
  | Upper()     | 将串转换为大写      |
  | LTrim()     | 去掉串左边的空格    |
  | RTrim()     | 去掉串右边的空格    |
  | Soundex()   | 返回串的Soundex的值 |
  | Substring() | 返回子串的字符      |
  | Length()    | 返回串的长度        |

  - Soundex：是一个将任何文本转换为描述其语音表示的字母数字模式的算法

  - ```mysql
    //一个顾客的名字实际叫 Y.Lie 但表里的存储的是Y.Lee，但这两个名字的发音相同，所以可以用Soundex
    
    SELECT cust_name, cust_contact
    FROM customers
    WHERE Soundex(cust_name) = Soundex('Y.Lie');
    //这样就可以用Y.Lie找出Y.Lee，解决了因为发音导致的错误
    ```

- 日期和时间处理函数

  - | AddDate()     | 增加一个日期（天，周等）       |
    | ------------- | ------------------------------ |
    | AddTime()     | 增加一个时间（时，分等）       |
    | DateDiff()    | 计算两个日期之差               |
    | Date_Add()    | 高度灵活的日期运算函数         |
    | CurDate()     | 返回当前日期                   |
    | CurTime()     | 返回当前时间                   |
    | Date()        | 返回日期时间的日期部分         |
    | Date_Format() | 返回一个格式化的日期或字符串   |
    | Day()         | 返回一个日期的天数部分         |
    | DayOfWeek()   | 对于一个日期，返回对应的星期几 |
    | Hour()        | 返回一个时间的小时部分         |
    | Minute()      | 返回一个时间的分钟部分         |
    | Month()       | 返回一个时间的月份部分         |
    | Now()         | 返回当前日期和时间             |
    | Second()      | 返回一个时间的秒部分           |
    | Time()        | 返回一个日期时间的时间部分     |
    | Year()        | 返回一个日期的年份部分         |

    - 日期的格式必须为 yyyy-mm-dd

  - ```mysql
    //提取一个某个日期的订单
    SELECT cust_id, order_num
    FROM orders
    WHERE Date(order_date) = '2005-09-01';	//正确
    WHERE order_date = '2005-09-01';		//可能出错，因为order_date存储的是DateTime类型的值，如 2005-09-01 11:20:32 ，只用日期可能会匹配不上，所以用Date()函数只匹配日期部分
    
    
    //提取某2020年1月中所有的订单
    SELECT cust_id, order_num
    FROM orders
    WHERE Year(order_date) = 2020 AND Month(order_date) = 1;
    ```

- 数值处理函数

  - | Abs()  | 返回一个数的绝对值 |
    | ------ | ------------------ |
    | Cos()  | 余弦值             |
    | Exp()  | 指数值             |
    | Mod()  | 取模               |
    | Pi()   | 圆周率             |
    | Rand() | 随机数             |
    | Sin()  | 正弦               |
    | Sqrt() | 平方根             |
    | Tan()  | 正切               |

### 9.  用聚集函数来汇集数据

- 聚集函数来获取表和表中数据的属性，如平均值，最值，行数，
- 关键字：distinct

- | AVG()   | 返回某列的平均值   |
  | ------- | ------------------ |
  | COUNT() | 返回某列的行数     |
  | MAX()   | 返回某列的最大值   |
  | MIN()   | 返回某列最小值     |
  | SUM()   | 返回某列所有值的和 |

  - AVG()：会忽略NULL值

    - ```mysql
      //不用WHERE子句时会计算整个列的均值，用WHERE就可只计算特定行的某些列值
      SELECT AVG(prod_price) AS avg_price
      FROM products
      WHERE vend_id = 1003;
      
      ```

  - COUNT() ：忽略为NULL的行，不计入

    - ```mysql
      //计算整个表的行数
      SELECT COUNT(*) AS num_cust
      FROM customers;
      
      //计算某一列的行数，只计入email行不为空的行
      SELECT COUNT(cust_email) AS num_cust
      FROM customers;
      
      ```

  - MAX()，MIN()

    - ```mysql 
      SELECT MAX(prod_price) AS max_price		
      SELECT MIN(prod_price) AS min_price
      FROM products;
      ```

  - SUM()：忽略值为NULL的行

    - ```mysql
      SELECT SUM(quantity) AS items_ordered
      FROM orderitems
      WHERE order_num = 20005
      ```

    - ```mysql
      //计算订单号为2005的订单中所有物品的总价钱
      SELECT SUM(item_price*quantity) AS total_price
      FROM orderitems
      WHERE order_num = 20005;
      ```

    - 以上5个函数都可以像这样对多个列进行运算

  - DISTINCT ：只计算不同的

    - ```MYSQL
      //计算供应商为1003的提供的所有商品的均价，去掉重复的
      SELECT AVG(DISTINCT prod_price) AS avg_price
      FROM products
      WHERE vend_id = 1003;
      
      ```

### 10. 分组数据

- 用聚集函数只能查看某一行或几行的数据，用分组数据就能看某一类的所有信息。

- 关键字： group by， having

- 创建分组

  - ```mysql
    //按照供应商的id来分组，计算每个供应商锁提供的商品总数。COUNT本来面向表，现在面向分组
    SELECT vend_id, COUNT(*) AS num_prods
    FROM products
    GROUP BY vend_id;			//GROUP BY放在WHERE后面
    
    //结果如下
    vend_id		num_prods
    1001		3
    1002		2
    1003		7	
    1004		2
    ```

  - NULL自成一组

- 过滤分组

  - having 过滤分组，where过滤行；

  - where使用的关键字having也可以使用

  - ```mysql
    //按照供应商的id来分组，计算每个供应商所提供的价格大于等于10的商品总数，同时只显示商品总数大于等于2的供应商，按照商品总数由小到大排列
    SELECT vend_id, COUNT(*) AS num_prods
    FROM products
    WHERE prod_price >= 10
    GROUP BY vend_id
    HAVING COUNT(*) >=2		//where, group by, having，order by 的组合使用
    ORDER BY num_prods;		//group by 的默认排序可能不是很恰当
    
    //结果
    vend_id		num_prods
    1005		2
    1003		4	
    ```

### 11. SELECT 子句顺序

- select --->  from ---> where --->  group by --->  having  ---> order by ---> limit

### 12. 子查询

- 需要根据另一张表中的数据作为数据源来检索数据

- 使用子查询时从外向里写，子查询可以先用死数据代替，当前的查询运行成功后再写子查询

- where 和子查询

  - ```mysl
    //根据某个订单中包含TNT2商品的订单号，查询购买此商品的用户。 也就是说列出订购物品TNT2的所有客户
    SELECT cust_id
    FROM orders
    WHERE order_num IN (SELECT order_num
                       	FROM orderitems
                       	WHERE prod_id = 'TNT2');
    ```

- 作为计算字段使用子查询

  - ```mysql
    //根据客户ID确定他所下的订单的总数
    SELECT cust_name,
    		cust_state,
    		(SELECT COUNT(*)
             FROM orders
             WHERE orders.cust_id = customers.cust_id) AS orders	
    FROM customers
    ORDER BY cust_name;
            	
           
    ```

  - 这里用了完全限定名，用来比较订单表中cust_id和当前正从客户表中检索的cust_id

### 13.联结表,表的别名inner / outer join...on

- 联结表数据库中不存在，只是在运行过程中存在

- 将两个表通过主外键联结起来显示匹配的数据

- 联结两个表

  - ```mysql
    //通过供应商id将供应商表和商品表联结起来，只显示供应商ID匹配的行
    SELECT vend_name, prod_name, prod_price
    FROM vendors, products		//用逗号隔开，下面的是另一种更推荐的语法
    WHERE vendors.vend_id = products.vend_id
    ORDER BY vend_name, prod_name;
    ```

  - 联结其实就是将一个表的每一行都要和另一张表的每一行匹配一次，也就是n×n行。在这n×n行中挑出供应商ID相同的行

  - 另一种写法：inner join ... on 

  - ```mysql
    SELECT vend_name, prod_name, prod_price
    FROM vendors INNER JOIN products
    ON vendors.vend_id = products.vend_id;
    ```

- 联结多个表：where and

  - ```mysql
    //找出购买TNT2商品的客户
    SELECT cust_name, cust_contact
    FROM customers, orders, orderitems
    WHERE customers.cust_id = orders.cust_id
    AND orderitems.order_num = order.order_num
    AND prod_id = 'TNT2';
    ```

- 高级联结

  - 给表起别名
    - ```MYSQL
      ...
      FROM customers AS c, orders AS o
      WHERE c.cust_id = o.cust_id
      ```

  - 自联结：利用表别名实现

    - 自己和自己联结：当某个供应商的货TNT2有问题，想要知道这个供应商的其他商品是否有问题时如何检索数据

    - ```mysql
      SELECT p1.prod_id, p1.prod_name	 //数据库知道要返回p1的列，而不是不知道返回p1还是p2的列
      FROM products AS p1, products AS p2
      WHERE p1.vend_id = p2.vend_id
      	AND p2.prod_id = 'TNT2';	//虽然p1和p2是一张表，但用p1显示，p2过滤
      ```

  - 自然联结：用通配符（select *）实现，对所有其他表的列使用明确的子集来完成

    - 有的列可能在不同的表中都会出现，所以联结时可能相同的列出现多次，但使用自然联结会排除多次出现，是每个列只出现一次

    - ```mysql
    SELECT c.*, o.order_num, o.order_date, oi.prod_id, oi.quantity, oi.item_price
      FROM customers AS c, orders AS o, orderitems AS oi
      WHERE c.cust_id = o.cust_id 
      	AND oi.order_numm = o.order_num
      	AND prod_id = 'FB';
      ```
  
  - 外部联结
  
    - 将一个表中的行与另一个表中的行相关联，有时需要包含那些没有关联行的那些行，如
    
      - 对每个客户下了多少订单进行计数，包括至今尚未下订单的客户
    
      - ```mysql
        SELECT customers.cust_id, orders.order_num	
        FROM customers LEFT OUTER JOIN orders	
        ON customers.cust_id = orders.cust_id;	
        
        //
        +---------+-----------+
        | cust_id | order_num |
        +---------+-----------+
        |   10001 |     20005 |
        |   10001 |     20009 |
        |   10002 |      NULL |
        |   10003 |     20006 |
        |   10004 |     20007 |
        |   10005 |     20008
        ```
    
    - left / right outer join 
    
      - 想要显示哪个表中所有的行就写它在outer join的左还是右
    
    - 带聚集函数的联结
    
      - ```mysql
        //显示所有客户所下的订单总数
        SELECT customers.cust_name, customers.cust_id, COUNT(orders.order_num)
        FROM customers left outer join orders
        ON customers.cust_id = orders.cust_id
        GROUP BY orders.cust_id;
        
        +----------------+---------+-------------------------+
        | cust_name      | cust_id | COUNT(orders.order_num) |
        +----------------+---------+-------------------------+
        | Mouse House    |   10002 |                       0 |
        | Coyote Inc.    |   10001 |                       2 |
        | Wascals        |   10003 |                       1 |
        | Yosemite Place |   10004 |                       1 |
        | E Fudd         |   10005 |                       1 |
        ```



### 14. 组合查询 union 

- 将多个select查询组合并显示在一张表中，所以select语句必须都显示同样的列

  - 在单个查询中从不同的表返回类似结构的数据
  - 对单个表执行多个查询，按单个查询返回数据

- ```mysql
  //显示所有价格小于等于5的商品和供应商为1001和1002的商品
  select prod_id, vend_id, prod_price
  from products
  where prod_price <=5
  union all			
  select prod_id, vend_id, prod_price
  from products
  where vend_id in (1001,1002)
  order by vend_id;
  ```

- union all ：显示所有的行，因为union会默认过滤重复的行

- order by 要放在最后一个select后

### 15. 全文本搜索 Match, Against, 

- 有的列中存储大量文本，这种列称为Fulltext ，需要在文本中按照关键字来查询时可以使用

- 数据库引擎：MyISAM 和 InnoDB

  - MyISAM 支持全文本搜索，InnoDB不支持

- 全文本搜索

  - match 来匹配存储文本的Fulltext列，可以匹配多个列，against来匹配目的索引

  - 原理：每一个句号代表一个完整的句子，系统对每个句子评估，如果有目的索引存在，那么索引在句子中越靠前等级越高，如果不存在，那么等级为0。最后按照等级从高到低排列有索引的句子

  - ```mysql
    //在note_text中搜索文本中含有rabbit的句子
    SELECT note_text
    FROM productnotes
    WHERE Match(note_text) Against('rabbit');
    ```

  - 扩展查询: with query expansion

    - 找出还可能与索引有关的句子，会匹配出更多的句子

    - 原理：两边扫描，第一遍找出索引，第二遍根据索引所在句子中出现多次的关键字再进行查询

    - ```mysql
      ...
      WHERE Match(note_text) Against('rabbit' WITH QUERY EXPANSION);
      ```

- 布尔文本查询 : IN BOOLEAN MODE，另一种全文本搜索，即使没有Fulltext也可以使用

  - | +    | 包含，词必须存在                                             |
    | ---- | ------------------------------------------------------------ |
    | -    | 排除，词必须不存在                                           |
    | >    | 包含，而且增加它的等级值                                     |
    | <    | 包含，但减少它的等级值                                       |
    | ( )  | 把词组成子表达式，允许这些子表达式作为一个组被包含，排除，排列等 |
    | ~    | 取消一个词的排序值                                           |
    | *    | 词尾的通配符                                                 |
    | " "  | 定义一个短语，与单个词的列表不一样，它匹配整个短语以便包含或排除这个短语 |

  - ```mysql
    ...
    //匹配包含词rabbit和bait的句子
    WHERE Mathch(note_text) Againist('+rabbit +bait' IN BOOLEAN MODE);
    //匹配包含rabbit和bait中至少一个的句子
    WHERE Mathch(note_text) Againist('rabbit bait' IN BOOLEAN MODE);
    //匹配短语rabbit bait而不是单词
    WHERE Mathch(note_text) Againist('"rabbit bait"' IN BOOLEAN MODE);
    //增加rabbit的等级，降低bait的等级
    WHERE Mathch(note_text) Againist('>rabbit <bait' IN BOOLEAN MODE);
    //匹配safe和combination，降低后者的等级
    WHERE Mathch(note_text) Againist('+safe +(<combination)' IN BOOLEAN MODE);
    ```

## 三. INSERT的使用

### 16. 插入数据 insert 

- 插入完整的行：insert into 表(列) values(值)

  - 不指定列：必须按照表中列的顺序插入，容易出错

  - 指定列：自己制定列，按照自己制定的列的顺序来插入，可以和表中列的顺序不一致

    - ```mysql
      INSERT INTO customers(cust_id,cust_name,cust_address,cust_city,cust_state,cust_zip,cust_country,cust_contact,cust_email)
      VALUES(NULL,'Sam','100 main street',...,'NULL','NULL');
      //客户ID是自增变量，所以填NULL
      ```

    - 可以只给部分列填值，只要该列允许为空

- 插入多行

  - ```MYSQL
    INSERT INTO 表1(列1，列2，列3...)
    VALUES(列1，列2，列3...);
    INSERT INTO 表1(列1，列2，列3...)		
    VALUES(列1，列2，列3...);
    ```

  - ```mysql
    //都往一个表中插入数据，且列的顺序一致时可以省略第二个insert into
    INSERT INTO 表1(列1，列2，列3...)
    VALUES(列1，列2，列3...), (列1，列2，列3...);
    ```

- 插入某些查询的结果：insert into 表（列）select 列 from 表

  - ```mysql
    INSERT INTO 表1(列1，列2，列3...)
    SELECT 列1，列2，列3... FROM 表2;
    ```

## 四. UPDATE和DELETE的使用

### 17.更新数据

- 更新表中特定行：update 表 set 列 = 值 where 条件

  - ```mysql
    UPDATE customers
    SET cust_name = 'tom'
    WEHRE cust_id = 10005;	//条件一般用主键
    ```

- 更新表中所有行：没有where，MySQL将用这个值更新表中所有的行

### 18.删除数据

- 删除之前先用select测试一下where的条件，delete不能撤回

- 删除表中特定行：delete from 表 where 条件

  - ```mysql
    DELETE FROM customers
    WHERE cust_id = 10005;
    ```

- 删除表中所有行：没有where ，MySQL将删除表中所有行，但不会删除表

  - truncate table : 删除表中所有的行，速度更快



## 五. 创建和操作表 create ，alter

- 创建表：create ,  auto_increment, primary key, engine, default

  - ```mysql
    CREATE TABLE 表名 (
    	cust_id 	 int 	  NOT NULL AUTO_INCREMENT,
        cust_address char(50) NULL     DEFAULT china,
        ...
        PRIMARY KEY(cust_id)
    )ENGINE=InnoDB;	
    ```

- 更新表：alter table，drop

  - 添加新列

    - ```mysql
      ALTER TABLE vendors
      ADD vend_phone CHAR(20);
      ```

  - 删除列

    - ```mysqql
      ALTER TABLE vendors
      DROP COLUMN vend_phone;
      ```

  - 定义外键

    - ```mysql
      ALTER TABLE orderitems
      ADD CONSTRAINT fk_orderitems_orders FOREIGN KEY (order_num) 
      REFERENCES orders(order_num);
      ```

- 删除表：drop table

  - ```mysql
    DROP TABLE customers;
    ```

- 重命名表：rename

  - ```mysql
    RENAME TABLE customers TO customers1,
    			 vendors TO vendors1;
    ```

## 六. 使用视图 view

- 视图：

  - 将sql语句封装起来，只有在使用时才检索，简化复杂的sql语句
  - 重新格式化检索出的数据
  - 主要方便检索

- 命令

  - 创建：create view 
  - 查看：show create view 视图名
  - 删除：drop view 试图名
  - 更新：两种方法
    - 先删除，然后重新创建
    - create or replace view

- 创建和使用视图

  - ```mysql
    //三个表联结，将能检索出已订购任意产品的所有客户的语句封装成视图
    CREATE VIEW productcustomers AS
    SELECT cust_name, cust_contact, prod_id
    FROM customers, orders, orderitems
    WHERE customers.cust_id = orders.cust_id
    ON orders.order_num = orderitems.order_num;
    
    //在视图中检索订购了产品TNT2的客户
    SELECT cust_name, cust_contact
    FROM productcustomers
    WHERE prod_id = 'TNT2';
    ```

    

  - 重新格式化检索出的数据

    - ```mysql
      //在供应商名字后加上所属国家
      CREATE VIEW vendorlocations AS
      	SELECT Concat(RTrim(vend_name), ' (', RTrim(vend_country), ')') AS vend_title
      	FROM vendors
      	ORDER BY vend_name;
      	
      //检索视图即可，便于重复使用
      SELECT * FROM vendorlocations;
      ```

  - 使用视图过滤不想要的数据

    - ```mysql
      //过滤掉email为空的行
      CREATE VIEW customeremailist AS
      SELECT cust_id, cust_name, cust_email
      FROM customers
      WHERE cust_email IS NOT NULL;
      ```

  - 使用视图与计算字段

- 更新视图

  - 不能更新视图的情况
    - 视图中有分组，联结，子查询，并，聚集函数，distinct，导出计算列

## 七. 使用存储过程 procedure，delimiter

- 概述

  - 为了以后的使用而保存的多条sql语句的集合，可以像函数一样传参数使用

- 展示存储过程

  - ```mysql
    SHOW CREATE PROCEDURE ordertotal;	//显示存储过程
    SHOW PROCEDURE STATUS LIKE 'ordertotal';   		
    //显示存储过程更详细的信息，包括作者是谁，创建时间，不加LIKE将显示所有存储过程
    ```

- 创建存储过程: create procedure 过程名 begin ... end

  - 无参数

    - ```mysql
      DELIMITER //
      CREATE PROCEDURE productpricing()		//创建一个计算商品价格平均值的存储过程
      BEGIN 
      	SELECT Avg(prod_price) AS priceaverage
      	FROM products;
      END//
      DELIMITER ;
      ```

  - begin和end之间使用了分号，所以要使用delimiter 将分号改为 //

  - 有参数：**in , out，into,declare，if, end if**

    - ```mysql
      DELIMITER //
      CREATE PROCEDURE ordertotal(	//创建一个计算订单总价值的存储过程,考虑加税的情况
      	IN onumber INT,				//onumber当作输入参数， ototal当作输出参数
          IN taxable BOOLEAN,			//用来判断是否给订单加税，1代表需要，0代表不需要
          OUT ototal DECIMAL(8,2)
      )COMMENT ‘obtain order total,optionally adding tax’ //用来解释存储过程
      BEGIN
      	--declare variable 			//用来声明变量
      	DECLARE total DECIMAL(8,2);
      	DECLARE taxrate INT DEFAULT 6;
      
      	--get the order total
      	SELECT Sum(item_price*quantity)
      	FROM orderitems
      	WHERE order_num = onumber
      	INTO total;
      	
      	--is this taxable?
      	IF taxable THEN				//判断语句
      		--taxable = 1
      		SELECT total+(total/100*taxrate) 
      		INTO total;
      	END IF;
          
          --save to out variable
          SELECT total INTO ototal;
      	
      END//
      DELIMITER ;
      ```

    - comment ：将来在命令行输入 **show procedure status** 时会显示出来，和注释一样

- 执行存储过程：call 过程名（），括号内存放参数

  - 无参数：无参数情况下只要调用就会显示结果

    - ```mysql
      CALL productpricing();	//直接显示商品的平均价格
      ```

  - 有参数：有参数情况下调用不会显示结果，需要再次检索

    - ```mysql
      CALL ordertotal(20005,1, @total);	//参数要用@
      ```

    - ```mysql
      SELECT @total;  //这次会显示订单号为20005的订单的加上税之后的总价钱
      ```

- 删除存储过程

  - ```mysql
    DROP PROCEDURE productpricing;
    DROP PROCEDURE IF EXISTS productpricing;	//也可以删除前先检验是否存在
    ```



## 八. 使用游标 cursor

- 概述

  - 类似于数组中的下标，用来遍历列中每一项
  - 和存储过程配合使用
  - 游标建立后在使用前要打开，使用后要关闭

- ```mysql
  -- 此存储过程不返回数据，但它创建和填充了一个表ordertotals
  CREATE PROCEDURE processorders()
  BEGIN
  	-- 声明变量
  	DECLARE done BOOLEAN DEFAULT 0;		//用来结束循环的标志位
  	DECLARE o INT;						//要查询的条件，代表订单号
  	DECLARE t DECIMAL(8,2);				//用来存储结果的变量，代表订单的总金额
  	
  	-- 声明某个列的游标
  	DECLARE ordernumbers CURSOR			//声明某一个列的游标
  	FOR 
  	SELECT order_num FROM orders;
  	
  	-- 声明 continue handler
  	DECLARE CONTINUE HANDLER FOR SQLSTATE '02000' SET done=1; 
  	-- 当条件SQLSTATE ‘02000’ 出现时，调用SET done=1。
  	-- SQLSTATE ‘02000’是一个未找到的条件，当repeat由于没有更多的行可供循环时出现这个条件 
  	
  	-- 在存储过程里创建一个表，
  	CREATE TABLE IF NOT EXIST ordertotals(
          order_num 	INT, 
          total 		DECIMAL(8,2)
      );
  	
  	-- 打开游标
  	OPEN ordernumbers;
  	
  	-- 循环遍历每一行
  	REPEAT
  		-- 获得订单号
  		FETCH ordernumbers INTO o;
  		
  		-- 获得这个订单的总金额
  		CALL ordertotal(o,1,t);
  		-- 这是上一小节的存储过程，用来计算一个订单的总金额
  		
  		-- 插入订单和总金额到ordertotals中，这个表刚刚创建
  		INSERT INTO ordertotals(order_num, total)
  		VALUES(o,t);
  		
  	-- 结束循环
  	UNTIL done END REPEAT;
  		
  	-- 关闭游标
  	 CLOSE ordernumbers;
  END;
  ```

- ```mysql
  SELECT * FROM ordertotals;
  -- 
  ```

- 

- declare 语句必须在声明游标之前定义

## 九. 使用触发器 trigger

- 概述

  - 当某个表发生更改时自动执行的语句，仅限delete insert update 三个语句使用，每个表最多支持6个触发器，每种语句有前后两个触发器可以使用
  - 如每当增加一个顾客时，都检查他的电话号格式是否正确；每当订购一个商品，都从库存数量中减去订购的数量
  - 

- 触发器需要的信息

  - 唯一的触发器名
  - 触发器关联的表
  - 触发器应该相应的活动
  - 触发器合适执行

- 创建触发器：create trigger...after / before

  - ```mysql
    -- 创建触发器 newproduct ，每次对products表进行插入操作前都会显示 ‘product added'语句
    CREATE TRIGGER newproduct 
    AFTER INSERT ON products 
    FOR EACH ROW SELECT 'product added';
    ```

  - 

- 使用触发器

  - insert

    - 在insert触发器中可以引用一个NEW的虚拟表，访问被插入的行

    - 对于是自动增量的列，NEW在使用 before inset时为0，使用 after insert 为自动生成的值

    - ```mysql
      -- order_num 是一个自动增量，使用after insert时NEW表中当前行的值时自动增加的
      CREATE TRIGGER neworder AFTER INSERT ON orders
      FOR EACH ROW SELECT NEW.order_num;
      ```

    - 

  - delete

    - 在delete触发器中可以引用一个OLD的虚拟表，访问被删除的行

    - OLD中得值是只读的，不能更新

    - ```mysql
      -- 每次删除orders表中数据时都会调用触发器deleteorder，在删除之前将被删除行的数据保存到archive_orders表中
      CREATE TRIGGER deleteorder BEFORE DELETE ON orders
      FOR EACH ROW
      BEGIN 
      	INSERT INTO archive_orders(order_num, order_date, cust_id)
      	VALUES(OLD.order_num, OLD.order_date, OLD.cust_id);
      END;
      ```

    - 可以用begin 和 end 来包括多个语句

  - update

    - 在update触发器中给你可以引用一个名为OLD的虚拟表访问更新之前的值，引用一个名为NEW的虚拟表访问新更新的值

    - 若使用before update时，NEW中的值可能也被更新

    - OLD中的值全都是只读的，不能更新

    - ```mysql
      -- 每次更新vendors表中数据时都会调用触发器updatevendor，在更新之前将更新的vend_state设置为大写
      CREATE TRIGGER updatevendor BEFORE UPDATE ON vendors
      FOR EACH ROW SET NEW.vend_state = UPPER(NEW.vend_state);
      ```

    - 

- 删除触发器

  - ```mysql
    DROP TRIGGER newproduct;
    ```

## 十. 管理事务处理

- 概述

  - 用来维护数据库的完整性，一批语句要么全执行，要么全不执行
  - 事务transaction：一组sql语句
  - 回退rollback：撤销指定sql语句的过程
  - 提交commit：将未存储的sql语句结果写入数据库表
  - 保留点savepoint：指事物处理中设置的临时占位符，可以对他发布回退，这和回退挣个事务不同

- 使用rollback：适用于insert update 和 delete操作， 不适用于 create drop操作

  - ```mysql
    SELECT * FROM ordertotals;			//ordertotals表还存在
    START TRANSACTION;					//开始事务
    DELETE FROM ordertotals;			//删除表，检索不到表
    SELECT * FROM ordertotals;			
    ROLLBACK;							//回滚到start transaction之前，撤销了刚才的操作
    SELECT * FROM ordertotals;			//又能搜索到ordertotals表了
    ```

  - 

- 使用commit

  - ```mysql
    -- 删除订单号为20001的订单时实现完全删除，所以要在order表和orderitems表一起删除
    START TRANSACTION;
    DELETE FROM orderitems WHERE order_num = 20001;
    DELETE FROM order WHERE order_num = 20001;
    COMMIT;		-- 只有两个delete都成功了才会提交，否则都不提交
    ```

    

- 使用savepoint

  - ```mysql
    SAVEPOINT delete1;		-- 设置保留点，回滚时回滚到保留点前
    ...
    ROLLBACK TO delete1;    
    ```

- 更改默认的提交行为

  - ```mysql
    SET autocommit=0;	-- mysql不再自动提交更改，只针对当前的连接，不是整个数据库
    ```

## 十一. 全球化和本地化



## 十二. 安全管理

- 概述

  - 通过限制用户的权限实现

- 获取用户列表

  - ```mysql
    USE mysql;
    SELECT user FROM user;
    ```

- 创建用户账号

  - ```mysql
    CREATE USER ben IDENTIFIED BY 'p@$$0rd' 
    -- 这是一个口令
    ```

- 重命名用户

  - ```mysql
    RENAME USER ben TO sam;
    ```

- 删除用户

  - ```mysql
    DROP USER sam;
    ```

- 查看用户权限

  - ```mysql
    SHOW GRANTS FOR sam;
    ```

- 设置访问权限

  - ```mysql
    -- 允许用户在某个表上具有select权限
    GRANT SELECT ON 数据库名.* TO 用户名;
    ```

- 撤销权限

  - ```mysql
    REVOKE SELECT ON 数据库名.* FROM 用户名;
    ```

- 更改口令

  - ```mysql
    SET PASSWORD FOR 用户名 = Password('@sdff');
    ```

  - 

## 十三. 数据库维护



## 十四. 改善性能



