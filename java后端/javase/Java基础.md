

#### 1.面向对象 √

```java
1.创建一个对象并调用其成员变量和成员方法的内存图
	栈，堆，方法区
2.成员变量都用private
  成员变量在方法外，类内，局部变量在方法内；
  成员变量有默认值，局部变量没有；成员变量在堆中，局部变量在栈中随方法一起；
  当成员变量和局部变量重名时，方法优先使用局部变量。若要区分，给本地变量前加this.成员变量；
  通过谁调用的方法，谁就是this;
3.private 修饰的变量或方法只能在本类中使用，其他类不能直接调用。
  对于私有变量需要用setter,getter方法间接访问，从而对数据的修改是否合理有判断；
  对于boolean型，是setter,is方法；
4.构造器 ： 
	若不写构造器默认送一个无参的构造器，若自己写了就不送了；
	构造器不能用return返回值；
	带参数的构造器在创建对象时可以传参，继而直接给创建的对象的成员变量赋值，省去了setter和getter方法，但还是要写setter,getter方法，因为日后还要改变成员变量。
	构造一个对象，其实就是在堆中给他分配了一块地方，对象名本身是地址；
5. 参数为某个对象或方法return返回某个对象
	参数是某个对象，其实就是把该对象在堆中的地址传过来
	返回某个对象，其实就是返回该对象在堆中的地址；
```

<img src="C:\Users\xpty\Pictures\Screenshots\屏幕截图(31).png"  />

#### Scanner 类型

- 概述
  - 用来控制输入
- 构造方法
  - public Scanner()：构造一个输入对象
    - Scanner sc = new Scanner(**System.in**); 从**键盘接受**数据
- 常用方法
  - public int **nextInt**()：将下一个从键盘输入的字符当作int型数据并接收
  - public String **next**()：将下一串从键盘输入的字符当作String型数据并接收

#### Random类型

- 概述
  - 生成随机数的类
- 构造方法
  - public Random()：
- 成员方法
  - public int **nextInt**()：获取一个范围为Int型的随机数
  - public int nextInt(int n)：生成的随机数大于0，小于n
    - 若想让随机数的范围是[1,n+1) ，那么给随机数加一即可

#### String类型

- 概述

  - 程序中所有字符串的字面值都作为此类的实例实现
  - 字符串内容不可改变，所以它可以共享使用
  - 字符串效果上等于char[] 数组，底层原理 byte[] 

- 构造方法

  - public String()：创建一个空白字符串
  - public String(char[] array)：根据**字符数组**中内容创建字符串
  - public String(byte[] array)：根据**字节数组**中内容创建字符串
  - String str = "hello"：直接创建，用**双引号**引起来的内容都是字符串

- 字符串常量池

  - ```java
    public static void main(String[] args) {	//字符串相等问题
        String str1 = "abc";
        String str2 = "abc";
        char[] chars = {'a','b','c'};
        String str3 = new String(chars);
    	char[] chars1 = {'a','b','c'};
    	String str4 = new String(chars1);
        
        System.out.println(str1 == str2);	//true
        System.out.println(str1 == str3);	//false
        System.out.println(str2 == str3);	//false
        System.out.println(str4 == str3);	//false
        
        String str5 = "hello";
        System.out.println(str5);	//hello
        str5 = "Java";
        System.out.println(str5);	//Java
        //str5引用的地址值变化，hello还存储在字符串常量池，Java是新创建的字符串
    }
    ```

  - 字符串创建时不同的底层原理：用双引号创建的字符串对象存储在堆中的字符串常量池中，用构造方法创建的字符串即使内容相同，在堆中也是不同的对象

  - ![](G:\google下载\未命名文件 (1).png)

- 常用方法

  - public boolean **equals**(Object obj)：比较字符串**内容**
  - Public boolean **equalsIgnoreCase**(String str)：无视字符大小写，比较字符串内容
  - public int **length**()：返回字符串长度
  - public String **concat**(String str)：将指定字符串拼接在当前字符串后并返回新的字符串对象
  - public char **charAt**(int index)：获取指定索引处的字符，从0开始
  - public int **indexOf**(String str)：查找参数字符串在本字符串中**首次**出现的索引位置，从0开始，若没有返回 -1
  - public String **substring**(**int index**)：截取从参数位置一直到末尾并返回新字符串，从0开始计数
  - public String substring(int begin, int end)：从begin截取到end，**左闭右开**
  - public **char[] toCharArray**()：将当前字符串拆分成字符数组并返回
  - public **byte[] getBytes**()：获取当前字符串的底层字节数组
  - public String **replace**(CharSequence oldString, charSequence newString)：将所有出现的老字符串替换为新的字符串，返回替换之后的新字符串。
    - CharSequence 是接口，此处的参数是**字符串**
  - public String[] **split(**String regex)：以所传参数为分隔符，将字符串分割成若干部分
    - String regex ：是正则表达式，当需要用英文句号 '.' 来分割时，添加两个反斜杠 "\\\\."

#### static 关键字

- 概述

  - 用Student类举例，每个学生的姓名，年龄，学号都不相同，但对所在班级来说，这个是**多个对象共享同一份数据**
  - 使用static关键字修饰的变量或方法不再属于对象，而**属于类**，凡是该类的对象都共享同一份
  - static方法可以通过类名直接调用，**不用创建对象**，所以静态方法只能调用静态方法
  - 非静态方法可以调用静态或非静态的变量和方法，但**静态方法**只能调用调用**静态方法和静态变量**
  - 本类中调用静态方法可以直接写方法名，不写类名也可以
  - 静态资源的加载先于非静态资源
    - 方法区中有一个静态区，专门存储静态资源，当通过类名调用变量或方法时会在静态区中查找，当通过对象调用变量或方法时会在堆中去查找

- 简单演示

  - ```java
    public static void main(String[] args) {
        Person p1 = new Person("张三",18);
        p1.setStudentclass("大一");	//static 成员变量设置一次，该类的所有对象都会随之改变
        System.out.println("学号："+p1.getId()+"姓名： "+p1.getName()+"年龄："+p1.getAge()+"年级："+p1.getStudentclass());
        
        Person p2 = new Person("李四",20);
        System.out.println("学号："+p2.getId()+"姓名： "+p2.getName()+"年龄："+p2.getAge()+"年级："+p2.getStudentclass());
    
    }
    ========================
    public class Person {
    	private String name;
    	private int age;
    	public static int id;			//静态变量的典型使用，id自增
    	public static String studentclass;
    	
    	public Person() {
    		super();
    		this.id = ++id;		//每创建一次对象id随之加一
    	}
    	public Person(String name, int age) {
    		super();
    		this.name = name;
    		this.age = age;
    		this.id = ++id;
    	}
    }
    ```

- 静态代码块

  - 概述

    - 当第一次使用该类时，静态代码执行唯一的一次
    - 静态资源的加载总是优先于其他资源
    - 用来一次性的给静态成员变量赋值

  - 格式

    - ```java
      static{
          ...
      }
      ```

    - 

#### 2.继承与多态 √

```java
一、继承extends： 目的：共性抽取，当对一个对象添加新功能时，可以不改变原有类，用新类继承原有类
    特点：子类就是一个父类，老师就是一名员工，子类会继承父类的成员变量和	成员方法；
    思想：对于成员变量和方法，用子类对象调用时，若子类有，优先用子类的，若没有则向上找；
    notes: 类可以多级继承，一个类可以有多个子类，但子类只有一个直接父类，
    1.父子类中成员变量重名，创建子类对象并访问时有两种方法：
    	直接方法：zi.变量名 调用子类的变量
    	间接方法：zi.method() 方法属于谁就优先用谁，没有则向上找
    2.成员变量：
      本类的成员变量： this.成员变量名
      父类的成员变量： super.成员变量名
    3.成员方法
       成员方法覆盖重写： 子类的方法与父类的方法相比，方法名，参数列表都要相同
       		子类方法的返回值范围要【小于等于】父类的返回值类型；
      		子类方法的权限必须【大于等于】父类方法的权限修饰符：
    			public>protected>无修饰符>private
        成员方法调用：看new的是谁，就优先用谁，没有则向上找。
        	 也就是说，优先用子类的方法，没有则用父类的
    4.构造器：创建子类对象时会先创建父类对象
    	子类构造器中有一个隐藏的默认的 super()方法来先构造父类对象，继而构造子类对象；
    	super()不传参调用无参构造器，传参调用有参构造器；
    	若父类只有传参构造器，子类只有无参构造器，则子类必须 super(param)调用父类的有参构器；
    	super()语句只能存在于构造器中，且只能出现一次，并且是第一条语句；this()同理
   		类中没有构造器，jvm会赠送一个无参构造器，反之，不会。	
    5.super的用法：	super.父类成员变量， super() ，super(param), super.method() ；
      this的用法： this.本类成员变量， this(), this(param), this.method();
	6.练习：收发红包

        
三、多态 ： 小明既是学生，也是孩子，还是人
    1.表现形式： 左父右子，父类引用指向子类对象
        父类名称 对象名 = new 子类名称();  对象向上转型，一定安全
		接口名称 对象名 = new 实现类名称();
	2.成员方法多态调用： 父类对象.成员方法， 若父类和子类成员方法重名，优先调用子类的方法；
        							   若子类没有对应方法，则用父类的方法	
    3.成员变量多态调用：直接访问： 父类对象.成员变量， 变量没有覆盖重写，所以用父类对象的变量
  					 间接访问： 父类对象.父类方法， 若子类没有重写父类方法，则使用父类方法，反							之，使用子类方法
    4.成员变量和成员方法多态调用对比：
        成员变量： 编译看（等号）左边，运行还看左边。 
        成员方法： 编译看（等号）左边，运行看右边。 父类对象.子类方法：父类不能调用子类方法，编译		 		  会出错
    5.多态的意义
        员工Employee类的继承类可以有会计，保安，秘书，保洁等等，若不用多态，则表现形式上等号左边	   都不相同，反之，相同，都为Employee类
    6.多态的弊端
        当调用子类的特有方法时，多态会失效，如 
        	Animal animal = new Cat();
			animal.catchMouse();  //编译错误
		解决方案： 对象向下转型--类型还原； 对象向下转型前记得要【对象 instanceof 类名】判断
            子类 子类对象 = (子类)父类对象，前提是子类，父类要是开始创建时的类；
            if(animal instanceof Cat){
                   Cat cat = (Cat)animal;  //(Cat)可以，(Dog)不可以
            }
     7.多态和接口练习： 
         接口对于实现类来说，它的地位有点类似父类对于子类；
         接口也可以做方法的参数，当调用该方法时，既可以用多态构造接口对象传参，也可以用构造实现类对象来传参数。传实现类对象时，系统自动向上转型。
         此处的练习，也可用抽象类和子类的关系实现，步骤一致
         
```

#### 抽象类

```java
二、抽象类和方法 : 只声明而不执行； 动物是抽象类，老虎是具体的类
    public abstract class animal{ 
    	public abstract void eat(); //有抽象方法的类必须是抽象类
        public void sleep(){...};   //抽象类也可以有普通方法
    }
	1.抽象类使用步骤：
		不能直接创建抽象类对象，创建animal类对象不能确定该对象能否实施某个方法
    	必须用一个子类来继承抽象父类, 创建一个cat类继承animal抽象类，并覆盖eat方法
   	 	子类必须覆盖父类中所有方法，否则子类必须为抽象子类
        利用子类实现
   		利用多态向上转型，构建一个抽象类对象
    2.抽象类-->抽象子类-->实体类
        若抽象子类没有覆盖所有抽象类中的抽象方法，则实体类需要覆盖它未覆盖的抽象方法；
        为什么抽象子类必须是抽象的，是因为抽象子类继承抽象类，且未覆盖所有抽象方法，抽象子类默认			有抽象类的抽象方法，所以抽象子类必须是抽象的
       
    3.notes
        抽象类中可以有构造方法，是供子类初始化父类对象用
```





#### 3.接口  √

```java
1，接口： 一种公共的规范标准，公用public；不能new接口，但可以new实现类并赋给接口对象，向上转型
2.接口的内容
	1.7 ：常量，抽象方法
    	常量：public static final int NUMBER = 10; 修饰符不写也可；常量名必须大写，用下划线分割
            接口中的变量必须赋值设为常量，在其他类中调用时： 接口名.常量名 ；
		抽象方法：public abstract void method(); 其中 public abstract 两者都可写或不写，都可以。
	1.8：同1.7+默认方法，静态方法
		默认方法： public default void method(){...} ,当接口增加了新方法时，不用改动原来已经声明该接口的实现类，只需将接口中的新方法设置为默认方法即可；
    			默认方法会被实现类继承下去；
    			接口的默认方法，可以通过接口实现类的对象调用，同时也可以被接口实现类进行覆盖；
    	静态方法：public static void method(){...}; 接口实现类不用声明静态方法
    			不能通过创建接口实现类的对象来调用接口中的静态方法，因为一个类可以实现多个接口，直接调用静态方法可能会有冲突，所以，直接用接口名.静态方法() 来调用；
				静态和对象没有关系，和实现静态的类有关
	1.9：同1.8+私有方法： 解决接口内多个方法中的重复代码
    	普通私有方法：private ElementType method(){...} 面向默认方法
    	静态私有方法：private static ElementType method(){...} 面向静态方法
3.接口使用步骤
   接口不能直接使用，必须有一个“实现类”来“实现”接口；
   接口的实现类必须重写接口中所有的抽象方法；
   创建实现类的对象
   note：若某个类没有重写接口中所有方法，则这个类必须是抽象类
4.notes
    接口内不能写静态代码块和构造器；
    一个类的直接父类是唯一的，但可以有多个接口；
    实现类实现的多个接口中有重名抽象方法出现，则实现类只需要覆盖一个即可；
    实现类没有覆盖所有接口中的所有抽象方法，则实现类必须是抽象类
    实现类实现的多个接口中有重名默认方法出现，则实现类要对冲突的默认方法覆盖重写；
    子类的直接父类中的方法和接口中的默认方法重名，优先使用父类的方法，若子类中覆盖重写了该方法，则使用子类方法。
    接口间继承：若某个子接口有多个父接口，且父接口间方法有重名现象。若抽象方法重名，子接口重写一个抽象方法即可；若默认方法重名，子接口也要重写，但格式必须为 default void method(){...}
default 不可少。
```

#### 4.异常  √

- **概念**：异常是一个类，产生异常就是创建该异常的对象并抛出，jvm遇到异常直接中断代码运行。
- **结构**：throwable类是所有错误或异常的超类，该类下有Error类和Exception类，Error类应避免出现，Exception类可以解决
- **分类**： 编译时期异常 Exception 和 运行时期异常 RuntimeException
- **处理方法**：
  - throws 异常： jvm遇到异常**直接终止代码运行**并打印异常信息
  - try/catch 代码块： 遇到异常后**继续执行后续代码**
- **关键字**：throw, throws, try/catch, finally
  - **throw** :  格式为   throw new 异常 
    - throw 关键字必须写在**方法内部**，用来对传过来的方法参数进行合法性判断
      - **Objects.requireNonNull(Object obj)**  : 判断该obj是否为空，简化操作
      - **Objects.requireNonNull(Object obj，String str) **:  判断该obj是否为空，若为空打印str内容
    - throw 关键字后边new的对象必须是Exception或Exception子类对象
    - throw 关键字抛出指定的异常对象，那么就必须要处理这个异常对象
      -  异常是**运行时异常**RuntimeException 或 其子类对象，可以不处理，**JVM自动处理**
      -  异常是**编译异常**（写代码时报错），用**throws 或 try/catch 处理**
  - **throws** :  放在方法后声明异常
    - throws 关键字必须写在方法声明处
    - throws 关键字后边声明的异常必须是Exception或Exception子类对象，若抛出的异常之间存在父子关系，则声明父类即可
    - **调用了一个声明抛出异常的方法，就必须处理该异常**
      - 要么继续使用throws 抛出异常，交给JVM处理
      - 要么用try/catch 自己处理
  - **try/catch** :  try 中写可能产生异常的代码， catch 中写异常的处理逻辑，工作中将异常信息记录到日志中
    - try中可能会抛出**多个异常**对象，那么就用**多个catch**来处理异常
    - try 中产生了异常，那么就会执行catch中语句，执行完继续执行catch后面的语句；若try中未产生异常，则不执行catch中语句，执行catch之后的语句
    - **异常处理的方法** 
      - **String getMessage ()** : 返回此throwable的简短描述
      - String toString() ： 返回throwable的详细消息字符串
      - **void printStackTrace()** ：JVM打印异常对象，异常信息最全面
  - **finally** : 放在catch后
    - 无论try语句块中是否出现异常，finally中语句**都会执行**；解决try中出现异常的语句后的语句无法执行问题
    - finally 不能单独使用，必须和try一起使用
    - finally 一般用于资源释放（回收），无论是否出现异常，都会要资源释放
    - finally 中避免出现return 语句；当try语句块中有return时，finally也有，返回finally中的
    - 父类方法抛出异常，则子类重写方法只能抛出**父类异常**或**父类异常的子类**，或**不抛出**异常
    - 父类方法未抛出异常，则子类也不能抛出异常，只能**自己捕获处理**
- **异常注意事项**
  - 产生多个异常时，捕获的使用策略
    - 多个异常分别处理 ： 用try/catch 分别处理
    - 多个异常一次捕获，多次处理：可能产生异常的代码全放在try中，多个catch分别捕获异常
      - catch中捕获的异常对象变量，如果有子父类关系，那么必须把子类异常变量放在父类上面，否则会报错。
        - 抛出的异常对象会从上到下赋值给catch中的异常变量，若先赋值给父类异常对象变量，则无法将子类异常对象赋值给异常变量
    - 多个异常一次捕获一次处理：  异常对象都赋给异常变量Exception

#### 5.Collection集合 √

##### 5.1 集合概述

- 单列集合

- 数组长度固定，可以存储基本类型或同一类型对象
- 集合长度可变，只可以存储对象，而且对象可以不一致。若集合未声明存储数据类型，那么字符，整型都可存储

##### 5.2 集合框架：

由上向下学习框架，

![1591018463933](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\1591018463933.png)

##### 5.3 Collection 常用方法

- public **boolean** **add**( E e)   往集合中添加对象
- public **void** **clear**()  清空集合
- public **boolean** **remove**(E e)  删除集合中指定对象
- public **boolean** **contains**(E e)  判断当前集合是否包含指定对象
- public **boolean isEmpty**()  判断当前集合是否为空
- public **int size**()  返回集合中元素个数
- public **Object[] toArray**()   把集合中元素存储到数组中，返回该数组

##### 5.4 Iterator接口

因为不同集合的遍历方法不一致，所以实现Iterator接口来遍历Collection所有集合

- 迭代：取元素之前先判断集合中有没有元素，有就取出，重复操作直到取出集合所有元素

- 常用方法

  - public **boolean hasNext**()  还有下一个元素返回true
  - public **Element next**()  返回下一个元素

- 使用步骤

  - Iterator<Element> it = coll.**iterator()**;   //用集合对象获取其迭代器对象

  - it.hasNext();

  - Element ele = it.next;    //循环这两步

  - ``` java
    while(it.hasNext()){
        Element ele = it.next();
        System.out.printl
    }
    ```


##### 5.5 foreach循环

- foreach替代Iterator接口，其只能被Collection或数组使用

##### 5.6 数据结构

- 红黑树
  - 概述：
    - 节点可以是红色或黑色
    - **根节点**和**叶子节**点是**黑**色的，其余节点为红色
    - 每个红色节点的子节点都是黑色的
    - 任何一个节点到其每一个叶子节点的所有路径上黑色节点数量相同

##### 5.7 List集合

- **带索引**的方法
  - public void **add**(int index, E element) ： index 为**元素插入后**的索引
  - public E **get**(int index)
  - public E **remove**(int index)
  - public E **set**(int index, E element)
- ArrayList 集合
  - 概述
    - 查找快，添加慢，底层是一个数组，添加一个元素会复制一次原数组
    - 实现不是同步的，需要并发控制
  - 继承 List 接口方法
- LinkedList 集合
  - 概述
    - 查找慢，添加快
  - 方法
    - public void **addFirst**(E e)   
    - public void **addLast**(E e)
    - public E **getFirst**()
    - public E **getLast**()
    - public boolean **removeFirst**()
    - public boolean **removeLast**()
    - public E **pop(**E e) ：弹出表头元素
    - public void **push**() : 往表头添加元素
    - public void **clear**()：清空表内元素
    - public boolean **isEmpty**()： 

##### 5.8 Set集合

- 概述
  
  - 不能同步
  
- 方法：没有索引，继承 Collection 的方法

- **HashSet**哈希表
  
  - 概述
    
    - 原理是 HashMap，但只能存一个值
    - 哈希值hashCode：一个十进制整数（对象逻辑上的地址），系统随机计算生成。
      - Object 类的  **int hashCode**()  ：得到该对象的哈希值；有的类重写了该方法
      - 哈希值相同的两个对象，物理地址不相等，用 ’==‘ 判断返回false
    - jdk 1.8 之前： 哈希表 = 数组 + 链表；jdk 1.8 哈希表 = 数组 + 链表 / 红黑树；链表长度超过8时，用红黑树
    - **不存储重复元素**的前提是存储元素对象本身**重写**了**hashCode和equals**方法
      - 某类对象重写hashCode方法后，属性相同的对象的**哈希值相同**
    
  - 存储元素过程
  
    - 先比较哈希值，再比较属性，若两次判断都为true，判定是同一个元素
  
    - ```java
      public static void main(String[] args) {
      	HashSet<String> hs = new HashSet<>();
          String s1 = new String("abc");
          String s2 = new String("abc");
          hs.add(s1);
          hs.add(s2);
          //hs调用add方法时，s1会先调用自身的hashCode方法计算自己的哈希值，然后调用自己equals方法，在集合中寻找是否有相同哈希值元素，若没有，则存储到集合中；s2同理。
      }
      ```
  
  - 存储自定义元素
  
    - 前提：重写该类的equals和hashCode方法
  
  - LinkedHashSet
  
    - 概述
      - 由**哈希表**（数组+链表/红黑树）+ **链表** 构成
      - 多出来的链表存储元素添加的顺序，所以LinkedHashSet是**有序**的
  
  - 可变参数
  
    - 概述
  
      - jdk 1.5 之后特性
      - 当方法的参数列表**数据类型已经确定**，但是**参数个数不确定**时使用
      - 一个方法只能有**一个可变参数**
      - 方法有多个参数时，可变参数放在**末尾**
  
    - 使用过程
  
      - 定义方法时使用，格式为  **Element  方法名（Element...参数名）**
  
      - ```java
        public static void main(String[] args) {
            int i = add(1,2)
        }
        
        public  static int add(int...arr){
            System.out.println(arr);		//1,2
            System.out.println(arr.length); //2
            return 0;
        }
        ```
  
      
  
    - 原理
  
      - 可变参数底层为一个数组，根据传递参数个数的不同，创建不同长度的数组存储参数，参数个数从0到多个
  
- TreeSet

##### 5.9 Collections

- 概述
  
  - 集合工具类，用来对集合进行操作
  
- 常用方法

  - public static <T> boolean **addAll**(**Collection**<T> c, T... elements)：往集合中添加一些元素
  - public static void **shuffle**(**List**<?> list)：打乱集合顺序
  - public static <T> void **sort**(**List**<T> list)：将集合中元素按照默认规则排序
  - public static <T> void **sort**(**List**<T> list, **Comparator<? super T>** )：将集合中元素按指定规则排序

- 使用过程

  - addAll

    - ```java
      public static void main(String[] args) {
          ArrayList<Integer> list = new ArrayList<>();
          Collections.addAll(list,1,2,3,4,5);
          System.out.println(list); 		//1,2,3,4,5
      }
      ```

  - shuffle

    - ```java
      Collections.shuffle(list);    //3,1,4,2,5
      ```

  - sort 与 Compar**able**接口

    - 数字按照**升序**排序，字符按照**自然顺序**排序

    - ```JAVA
      public static void main(String[] args) {
          ArrayList<Integer> list = new ArrayList<>();
          Collections.addAll(list,"b","c","a");
          Collections.sort(list);
          System.out.println(list); 		//a,b,c
      }
      ```

    - **自定义对象的排序**

      - sort 方法使用前提： 被排序集合里**存储的元素**必须**实现Comparable接口**，并**重写compareTo方法**

      - **Comparable 接口**的排序规则

        - this.方法 -  参数.方法    ----->     升序
        - 参数.方法  -  this.方法 ------>    降序

      - 使用过程

        - ```java
          public class Persion implements Comparable{
              String name ;
              int age;
              
              ...	//getter,setter等各种方法
                  
              @Override    
              public int compareTo(Person p){
                  return this.getAge() - p.getAge();  //按年龄升序排列
                  //return p.getAge() - this.getAge(); 按年龄降序排列
              }
          }
          
          public static void main(String[] args) {
          	...
              Collections.sort(person);  //按年龄升序排列
          }
          ```

  - sort 与 **Comparator 接口**

    - 综述

      - 两者区别
        - Comparable ：自己和别人比较，需要实现接口，重写比较方法
        - Comparator： 找一个第三方裁判，排序时把他叫过来，说好规则即可
      - 排序规则
        - 前 - 后  ------>  按升序
        - 后 - 前  ------>  按降序

    - 实现过程

      - 按单个规则排序

      - ```java
        public static void main(String[] args) {
        	ArrayList<Integer> list = new ArrayList<>();
            ...
            Collections.sort(list, new Comparator<Integer>(){
                @Override
                public int compare(Integer o1, Integer o2){
                    return o1-o2;   //按升序排列
                    //return o2-o1;   按降序排列
                }
            });  
        }
        //自定义对象排序同理, return 的写法和 compareTo 方法一样
        ```

      - 按多个规则排序

      - ```java
        public static void main(String[] args) {
        	ArrayList<Person> list = new ArrayList<>();
            ...
            Collections.sort(list, new Comparator<Person>(){
                @Override
                public int compare(Person o1, Person o2){
                    //按年龄排序
        			int result = o1.getAge() - o2.getAge();
                    //若年龄一致时，按姓名首字母排序
                    if(result == 0)
                       result = o1.getName().charAt(0) - o2.getName().charAt(0);
                    return result;
                }
            });  
        }
        ```

#### Arrays 工具类

- 静态方法
  - public static String toString(T[] a)：将参数数组变成字符串
  - public static void sort(T[] a)：按照默认顺序进行排序，数值从大到小，字符串按自然顺序
  - public static void sort(T[] a,  Comparator<? super T> c )：用自定义的排序规则对参数数组排序

#### Math 工具类

- 静态成员变量
  - public static final double PI ：近似的圆周率常量

- 静态方法
  - public static double abs(double num)：获取参数的绝对值
  - public static double ceil(double num)：向大取整获取参数的值
  - public static double floor(double num)：向小取整获取参数的值
  - public static long round(double num)：四舍五入获取参数的值，返回的是long型，没有小数点




#### 6.泛型 

- 格式和使用过程： 

  - **泛型类的使用**

    - 定义一个使用泛型的类

    - ```java
      public class Generics<E>{			//所有原来为对象类型的地方都置换为 E 
          private E name;
          public void setName(E name){}
          public E getName(){}
      }
      ```

    - 使用一个使用泛型的类

    - ```java
      Generics<String> arr1 = new Generics<>();   
      Generics<Integer> arr2 = new Generics<>();  //同一个类可以创建存储不同类对象的集合
      ```

  - **泛型方法的使用**

    - 定义一个使用泛型的方法

    - ```java
      public class GenericsMethod{	//泛型放在修饰符后，返回值前		
          public <M> void method1(M m){ 		  //普通方法用 M
              System.out.println(m);
          }
          public static <S> void method2(S s){  //静态方法用 S
              System.out.println(s);
          }
      }
      ```

    - 使用一个使用泛型的方法

    - ```java
      GenericsMethod gm = new GenericsMethod();
      gm.method1(10);
      gm.method1("abc");
      gm.method1(true);		//静态方法method2同理
      
      GenericsMethod.method2(10);   //静态方法可以直接通过类名调用
      GenericsMethod.method2("abc");
      GenericsMethod.method2(true);
      ```

    ![img](https://img-blog.csdn.net/20181010111503937?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3dlaXhpbl80MjA3MTg3NA==/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

  - **泛型接口的使用**

    - 定义一个使用泛型的接口

    - ```java
      public interface GenericsInterface<I> {
          public abstract void method1(I i);
      }
      ```

    - 定义一个使用泛型的接口的实现类，有两种方式

    - ```java
      public interface GenericsInterfaceImpl implements GenericsInterface<String>{
          public abstract void method1(String str){...}
      }//创建接口实现类时，定义接口的泛型类型
      ```

    - ```java
      public interface GenericsInterfaceImpl<I> implements GenericsInterface<I>{
          public abstract void method1(I i){...}
      }//创建接口实现类时，跟着接口的泛型
      ```

    - 使用一个使用泛型的接口的实现类

    - 第一种接口实现类正常使用

    - 第二种接口实现类自己定义泛型类型

- **好处** 

  - 把运行期异常提前到编译期异常，写代码时就可以判断是否正确。
  - 可以创建不同类型的

- **泛型通配符**

  - 格式：  用 ？ 表示，如 **ArrayList<?> array ** 

  - 用途： 方法的参数用通配符时，可以接收同一类集合，但集合存储对象不同的集合

  - 演示

  - ```java
    ArrayList<String> array1 = new ArrayList<>();
    array1.add("a");
    ArrayList<Integer> array2 = new ArrayList<>();
    array2.add(1);
    
    public void method(ArrayList<?> array){
        //遍历array元素
    }
    ```

  - 注意： 创建对象时不能用，只能用在方法接收参数时。

  - **泛型的上下限**

    - <? extends E>   泛型只能是E的子类或E本身
    - <? super E>   泛型只能是E的父类或E本身

#### Object类及常用API √

##### Object类

- **toString** 方法： 若没有重写toString方法，则打印类对象的结果是该对象的**堆地址**；若重写了，则打印结果格式为：  类名｛成员变量 = 对象的成员变量值｝

- **equals **方法：

  -  基本数据类型用 '==' ,比较的是值，引用数据类型用 equals 比较的是地址。

  - 重写 equals 方法：因为比较两个对象的地址无意义，需要比较两个对象的属性

    - ```java
      public boolean equals(Object obj){ //重写Person类的equals方法
          if(obj==this)			//传过来的是他自己
              return true;
         	if(obj==null)			//传过来的是null
              return false;
          if(obj instanceof Person){		//传过来的是否是同一个类
              Person p = (Person)obj; 		//先向下转型
          	boolean b = this.name.equals(p.name) && this.age.equals(p.age);
         		return b;
          }
          return false;
      }
      ```

    - **Objects** 类的equals 方法：  解决引用对象为空的问题

      - ```java
        public boolean equals(Object a, Object b){
            return (a==b) || (a!=null && a.equals(b));
        }
        ```

##### Date类

- 概述：
  -  表示特定的瞬间，精确到毫秒； 1000 ms = 1 s;   
  - 时间原点为1970.1.1的0点0分0秒，
  - 计算机时间用毫秒存储，当前日期通过当前瞬间到时间原点的差值转换为日期来确定
  - System.currentTimeMillis() 方法获得当前瞬间的毫秒数
- 构造方法
  - Date() ：构造一个日期对象
  - Date(Long date) ：所传参数为毫秒数，构造一个该参数对应的日期对象
- 成员方法
  - getTime() ：获取当前瞬间的**毫秒值**

##### DateFormat类

- 概述
  - 是日期/时间格式化子类的**抽象类**，完成日期和文本之间的转换。使用时需要用它的**子类 SimpleDateFormat**
  - 格式化： 日期 --> 文本
  - 解析:  文本 --> 日期
  
- 成员方法
  - **public String format(Date date)** ：按指定格式把Date日期格式化为指定字符串
  - **public Date parse(String source)**：把符合格式的字符串，解析为Date日期
  
- **SimpleDateFormat** 

  - 构造方法

    - **SimpleDateFormat(String pattern)** : 用给定的模式pattern和默认语言环境的日期格式符号构造对象

    - String pattern ： 

      - | y    | 年   |
        | ---- | ---- |
        | M    | 月   |
        | d    | 日   |
        | H    | 时   |
        | m    | 分   |
        | s    | 秒   |


      - pattern 可由上表字符**任意组合**，如“yyyy-MM-dd HH:mm:ss”， "y年M月d日 H时m分s秒"

- 使用过程

  - 将日期格式化为文本

  - ```java
    public static void main(String[] args) {
    		SimpleDateFormat sdf = new SimpleDateFormat("y年m月d日");
    		Date date = new Date();
    		String time = sdf.format(date);
    		System.out.println(time);
    	}
    ```

  - 将文本解析为日期

  - ```java
    public static void main(String[] args) throws ParseException {
    		SimpleDateFormat sdf = new SimpleDateFormat("yyyy-MM-dd");
    		Date date = sdf.parse("2020年3月3日");		//该日期须符合构造时的格式
    		System.out.println(date);
    	}
    ```

##### Calendar类

- 综述

  - 用来替换Date类，包含更丰富的时间信息
  - Calendar日历类是一个**抽象类**，有一个抽象方法 getInstance

- 静态方法

  - public static **Calendar getInstance()** ：使用默认时区和语言环境获得一个日历对象

- 常用方法

  - public **int get(int field)** ：返回给定日历字段的值
  - public **void set(int field, int value)** ：将给定的日历字段设置为给定值
  - public **abstract void add(int field, int amount)** ：根据日历规则，为给定的日历字段添加或减去指定的时间量
  - public **Date getTime()** ：将当前Calendar对象转换为Date对象

- 方法使用过程

  - int **get**(int field)

    - Calendar类中有许多int型的静态变量，如YEAR,MONTH,DAY等，不同的filed指定获取不同的日历字段

    - ```java
      public static void main(String[] args) {
          Calendar ca = Calendar.getInstance();
          int year = ca.get(Calendar.YEAR);   //通过类名调用静态变量
          System.out.println(year);
      }
      ```

    - 注意： 西方的月份0--11， 东方的月份1--12， 使用时需要加一

  - void **set**(int field, int value)

    - ```java
      public static void main(String[] args) {
          Calendar ca = Calendar.getInstance();
          ca.set(Calendar.YEAR, 2021)
          System.out.println(ca.get(Calendar.YEAR)); //需要用get方法获取Calendar.YEAR
      }
      ```

    - void set(int field, int field, int field)

      - 一次性设置年，月，日的属性

  - void add(int field, int amount)

    - ```java
      public static void main(String[] args) {
          Calendar ca = Calendar.getInstance();
          ca.add(Calendar.YEAR, 10);   //amount 为正，加
          ca.add(Calendar.MONTH, -1);	  // amount 为负，减
          System.out.println(ca.get(Calendar.YEAR));
          System.out.println(ca.get(Calendar.MONTH));
      }
      ```

  - Date getTime()

    - ```java
      public static void main(String[] args) {
          Calendar ca = Calendar.getInstance();
        	Date date = ca.getTime();
          System.out.println(date);
      }
      ```

##### System类

- 综述

  - 大量静态方法获取与系统相关或系统级操作

- 常用方法

  - **public static long currentTimeMillis()** ：返回以毫秒为单位的当前时间
  - **public static void arraycopy(Object src, int srcPos, Object dest, int destPos, int length)** ：将数组中指定长度的数据拷贝到另一个数组中

- 使用过程

  - long currentTimeMillis()

    - ````java
      public static void main(String[] args) {
          System.out.println(System.currentTimeMillis());
      }
      ````

  - void arraycopy(src, srcPos, dest, destPos, length)

    - ```java
      public static void main(String[] args) {
      	int[] array1 = {1,2,3,4,5};
      	int[] array2 = {6,7,8,9,10};
      	System.arraycopy(array1, 0, array2, 1, 3); 	 //起始位置从0开始算
          System.out.println(Arrays.toString(array2)); //[6,1,2,3,10] 
      }
      ```

##### StringBuilder类

- 综述

  - String 类对象是常量，创建之后不能改变，底层是 private **final byte[]** value,  字符串在组合时会产生多个数组，造成资源浪费。
    - String  str = "a"+"b"+"c";    在执行时会产生5个数组分别存储："a","b","c","ab","abc"
  - StringBuilder 叫**字符串缓冲区**，StringBuilder 类对象创建后可以改变，底层是一个**可变的数组**，长度为**16**，长度不够就再加16
    - StringBuilder str = "a"+"b"+"c";     在执行时只产生1个数组将字符串存储起来

- 构造方法

  - StringBuilder()	: 构造一个空的StringBuilder对象
  - StringBuilder(String **str**) ：构造并**初始化**一个StringBuilder对象，对象值为str； String-->StringBuilder 

- 常用方法

  - **public StringBuilder append(Object obj)**： 在调用对象的末尾附上参数，并**返回自身对象**，可以链式编程
  - **public String toString()** ：将StringBuilder 类对象转化为String对象

- 使用过程

  - StringBuilder **append**()

    - ```java
      public static void main(String[] args) {
          StringBuilder sb = new StringBuilder();
          sb.append("abc").append("edf").append(1).append(true);	//链式编程
          System.out.println(sb);	 //abcedf1true
      }
      ```

  - String **toString**()

    - ```java
      public static void main(String[] args) {
          StringBuilder sb = new StringBuilder("abc"); //String-->StringBuilder
          String str = sb.toString();// StringBuilder-->String
      }
      ```

##### 包装类

- 综述

  - Java提供两个类型系统，基本类型和引用类型。引用类型可以有更多操作，想要将基本类型当作引用类型来使用时，就可以使用它的包装类

  - | 基本类型 | 对应的包装类  |
    | -------- | ------------- |
    | byte     | Byte          |
    | short    | Short         |
    | int      | **Integer**   |
    | long     | Long          |
    | float    | Float         |
    | double   | Double        |
    | char     | **Character** |
    | boolean  | Boolean       |

- 装箱与拆箱

  - 装箱： 基本类型-->包装类
  - 拆箱： 包装类-->基本类型

- 构造方法（装箱）

  - Integer
    - **Integer(int value)** ： 构造一个新分配的 Integer 对象， 表示指定的 int 值
    - **Integer(String s)**： 构造一个新分配的 Integer 对象，表示 String 参数所指的 int 值

- 静态方法（装箱）

  - Integer

    - **static Integer valueOf(int i)**   返回一个表示指定的 int 值的 Integer 实例

    - ```java
      public static void main(String[] args) {
       	Integer in = Integer.valueOf(5);
      }
      ```

    - **static Integer valueOf(String s)**  返回保存指定的 String 的值的 Integer 对象

- 成员方法（拆箱）

  - Integer

    - **int intValue()** 以 int 类型返回该 Integer 的值

    - ```java
      public static void main(String[] args) {
       	Integer in = Integer.valueOf(5);
          int i = in.intValue(); 
      }
      ```

- 自动装箱和自动拆箱（1.5 之后）

  - ```java
    public static void main(String[] args) {
     	ArrayList<Integer> array = new ArrayList<>();
        array.add(1);			//自动装箱
        int n = array.get(0);	//自动拆箱
    }
    ```

- 基本类型和字符串之间的转换

  - 基本类型-->字符串

    - **基本类型的值 + ”...“**   ： 输出语句常用

    - 包装类的静态方法 toString(参数)， 不是Object类的toString()

      - static **String** **toString(int i)** ： 返回一个表示指定整数的 String 对象

      - ```java
        public static void main(String[] args) {
        	String s = Integer.toString(100);
        }
        ```

    - String类的静态方法 valueOf(参数)

      - static **String valueOf(int i)** ：返回 int 参数的字符串表示形式

      - ```java
        public static void main(String[] args) {
        	String s = String.valueOf(100);
        }
        ```

  - 字符串-->基本类型

    - 使用包装类的静态方法 **parseXxx("字符串")**

      - Integer类： static **int parseInt(String s)**

      - ```java
        public static void main(String[] args) {
        	int i = Integer.parseInt("100");
        }
        ```

      - Double类： static **double parseDouble(String s)**

#### File类

- 概述

  - 文件和目录路径名的抽象表示形式，直接输出File对象显示的是**字符串**
  - Java把电脑中的文件和文件夹封装成一个file类，用来操作文件和文件夹

- 静态成员变量

  - static **String pathSeparator**：与系统有关的**路径分隔符**，用字符串表示
  - static char pathSeparatorChar：同上，用字符表示
  - static **String separator**：与系统有关的默认**名称分隔符**，用字符串表示
  - static char separatorChar：同上，用字符表示

- 静态成员变量演示

  - ```java
    String pathSeparator = File.pathSeparator;
    System.out.println(pathSeparator);		//输出 ;    
    
    String separator = File.separator;
    System.out.println(separator);			//输出 \	  
    ```

    - |        | pathSeparator        | separator                                 |
      | ------ | -------------------- | ----------------------------------------- |
      | winds  | ;（分号）            | \（反斜杠）                               |
      | linux  | :（冒号）            | /（正斜杠）                               |
      | wins:  | C:\Java\jdk1.8.0_231 | C:\Java\jdk1.8.0_231;C:\Java\jdk1.8.0_231 |
      | linux: | C:/Java/jdk1.8.0_231 | C:/Java/jdk1.8.0_231:C:/Java/jdk1.8.0_231 |

    

  - 作用：在写**操作路径是不能写死**，要用分隔符来替换 ；和 \

    - ```java
      "C:"+File.separator+"Java"+File.separator+"jdk1.8.0_231"+File.pathSeparator+"C:"+File.separator+"Java"+File.separator+"jdk1.8.0_231"
      ```

- 绝对路径和相对路径

  - 绝对路径：一个完整的路径，以盘符开始
  - 相对路径：一个完整路径的部分，简化书写
  - 注意
    - 路径不分大小写
    - wins下名称分隔符用反斜杠，但反斜杠是转义字符，所以用**两个**反斜杠代表**一个**普通反斜杠

- 构造方法

  - File(String pathname)：将给定路径字符串转换为File对象
  - File(String parent, String child)：将路径分为**父路径和子路径**来创造，方便在不同父路径下创建同名子文件
  - File(File file, String child)：父路径为File对象可以调用方法
  - 注意
    - 路径可以是文件或文件夹结尾
    - 路径可以是相对也可是绝对路径
    - 路径**可以不存在**，此处只是构建对象，不考虑真实情况

- 构造方法使用过程

  - ```java
    File file = new File("G:\\Users\\Administrator\\eclipse-workspace\\JavaBasics");
    System.out.println(file); //把双斜杠变单斜杠
    //File括号内自动将单反斜杠转换为双反斜杠
    ```

  - ```java
    File file = new File("G:\\","a.txt");
    System.out.println(file);  //G:\a.txt
    ```

  - ```java
    File parent = new File("C:\\");
    File file = new File(parent, "hello.java");
    System.out.println(file); //C:\hello.java
    ```

- 常用方法

  - File类**获取**功能的方法
    - public **String getAbsolutePath**()：无论File对象是绝对还是相对路径，都会返回绝对路径
    - public **String getPath**()：将File转换为路径字符串，是绝对就打印绝对，是相对就打印相对
    - public **String getName**()：返回此File表示的**文件或文件夹**的名称
    - public **long length**()：返回此File表示的**文件大小**，以字节为单位。若路径指向文件夹或空，返回不确定的值或0
  - File类**判断**功能的方法
    - public boolean **exists**()：此File对象是否真实存在
    - public boolean **isDirectory**()：此File对象是否为目录
    - public boolean **isFile**()：此File对象是否为文件
  - File类**创建删除**功能的方法
    - public **boolean createNewFile**()：该名称唯一时创建**新的空文件**，路径不对也会返回false
    - public **boolean delete**()：删除此File对象代表的文件或文件夹，文件有内容删除失败
    - public **boolean mkdir**()：创建由此File表示的**单级**目录
    - public **boolean mkdirs**()：创建由此File对象表示的**多级**目录，包括任何必需但**不存在的父目录**
  - File类**遍历文件夹**功能的方法
    - public **String[] list**()：返回一个字符串数组，存储该**文件夹**下所有子文件
    - public **File[] listFiles**()：返回一个**File数组**，将该文件夹下所有子文件以**File对象存储**起来，可以用来**多级遍历文件**
    - public **File[] listFiles**(**FileFilter filter**)：FileFilter 是接口，该接口返回boolean值，用来筛选需要的文件或文件夹
    - public **File[] listFiles**(**FilenameFilter filter**)：FilenameFilter 是接口，该接口返回boolean值，用来筛选需要的文件或文件夹

- 常用方法使用过程

  - File类获取功能的方法

    - ```java
      File file2 = new File("File");  
      String absolutePath = file2.getAbsolutePath();
      System.out.println(absolutePath);
      //G:\Users\Administrator\eclipse-workspace\JavaBasics\File
      ```

    - ```java
      File file2 = new File("File");  
      String path = file2.getPath();
      System.out.println(path);  //File
      ```

    - ```java
      File file2 = new File("G:\\Users\\Administrator\\eclipse-workspace\\JavaBasics\\File");  
      String name = file2.getName();
      System.out.println(name);		//File
      ```

    - ```java
      File file3 = new File("C:\\Users\\xpty\\Desktop\\ps\\00.jpg");  
      System.out.println(file3.length());	  //6897
      ```

  - File类判断功能的方法

    - 先用exists判断路径指定的文件或文件夹是否存在，若不存在都会判断为false

    - ```java
      File file = new File("C:\\Users\\xpty\\Desktop\\ps");
      if(file.exists()) {		
          System.out.println(file.isDirectory());
          System.out.println(file.isFile());
      }
      ```

  - File类创建删除功能的方法

    - ```java
      File file = new File("C:\\Users\\xpty\\Desktop\\ps\\1.txt");
      try {
          System.out.println(file.createNewFile());  // true
      } catch (IOException e) {
          e.printStackTrace();
      }
      ```

    - ```java
      File file1 = new File("C:\\Users\\xpty\\Desktop\\ps\\dir1");
      System.out.println(file1.mkdir());   //true， 创建dir1文件夹
      File file2 = new File("C:\\Users\\xpty\\Desktop\\ps\\dir1\\dir2\\dir3");
      System.out.println(file2.mkdirs());	
      //true， 在dir1下创建dir2文件夹，在dir2下创建dir3文件夹
      
      System.out.println(file2.delete());	 //true,删除dir3成功
      System.out.println(file1.delete());	 //false,删除dir1失败，因为含有dir2
      ```

  - File类遍历文件夹功能的方法

    - ```java
      File file = new File("G:\\Users\\Administrator\\eclipseworkspace\\JavaBasics");
      String[] strs = file.list();
      for(String str : strs) {
          System.out.println(str);  //输出文件夹下的文件或文件夹名称
      }
      
      File[] files = file.listFiles();
      for(File f : files) {
          System.out.println(f);	//File对象的绝对路径
      }
      ```

- 递归

  - 两大要素
    - 递归结束的条件
    - 递归的目的 

- **FileFilter** 和 **FilenameFilter** 接口

  - FileFilter接口：用于过滤文件（File对象）

    - public abstract **boolean accept(File pathname)**
      - pathname：用 listFiles方法遍历得到的文件对象

  - FilenameFilter 接口：用于过滤文件名称

    - public abstract **boolean accept(File dir, String name)**
      - dir：被遍历File 对象
      - name：使用 listFiles 方法遍历该File对象得到该对象下的文件、文件夹的名称

  - **手动**创建这两个接口的**实现类**，重写accept 方法，定义过滤规则

  - 使用过程

    - ```java
      public static void main(String[] args) {
          File file = new File("G:\\新建文件夹 (2)\\abc");
          getAllFiles(file);
      }
      
      public static void getAllFiles(File file) {	//
          File[] files = file.listFiles(new FileFilterImpl());
          for(File f : files) {
              if(f.isDirectory())
                  getAllFiles(f);
              else
                  System.out.println(f.getName());
          }
      }
      
      public class FileFilterImpl implements FileFilter{
      	@Override
      	public boolean accept(File pathname) {
      		if(pathname.isDirectory())
      			return true;
      		return pathname.getName().toLowerCase().endsWith(".java");
      	}
      }
      ```

    - listFiles(FileFilter filter)

      - 该方法在将该路径包含的文件或文件夹根据重写的accept方法进行**筛选**，符合条件的装入File数组中

    - FileFilterImpl

      - FileFilter 的实现类，对文件或文件夹进行判断，符合条件的返回true，

#### 6.Map集合√

- 概述
  - **与Collection 并列**
  - 双列集合，键（key) 值（value）一一对应
  - key  和 value 可以相同， 但key 之间不能重复
  - **不是同步的**，需要考虑并发
  
- Map常用方法
  - public v **put(K key, V value)**：添加键值对到Map集合，**key 可以为 null**
  - public v **remove(Object key)**：删除Map集合中指定键值对，**返回**被删除元素的**值**
  - public v **get(Object key)**：根据键，查找值
  - boolean **containsKey(Object key)**：判断集合是否含有参数指定的键
  - public **Set<k> keySet()**：获取Map集合中所有的键，存储到Set集合中，进而遍历所有值
  - public **Set<Map.Entry<k,v>> entrySet()**：获取Map集合中所有的键值对对象的集合
  
- 方法使用过程
  - put
    
    - 存储键值对时，若key不重复，返回值 V 是**null**
    - 存储键值对时，若key重复，使用新的value替换原有的，并返回**被替换**的value
    
  - remove
  
    - key存在时，V返回**被删除**的值
    - key不存在时，V返回NULL
  
  - get
  
    - key存在时，V返回key对应的value
    - key不存在时，V返回NULL
  
  - contains
  
    - key存在时，返回true
    - key不存在时，返回false
  
  - keySet：遍历Map集合中所有的值
  
    - 先用 keySet 方法将所有的 key 存储到 set 集合中，然后遍历 set 集合，根据键找值
  
    - ```java
      public static void main(String[] args) {
          Map<String,String> map = new HashMap<>();
          ...
      	Set<String> keys = map.keySet();
          for(String key : keys) {
              String value = map.get(key);
              System.out.println(value);
          }
      }
      
      ```
  
  - entrySet
  
    - 概述
  
      - Entry
        - 当Map集合创建时，也会创建一个Entry对象，用来记录所有键值对的映射关系---**结婚证**。
      - 和keySet 方法原理类似，先将所有 entry 对象存储到 Set 集合中，然后根据 entry 对象的方法查找键值对
  
    - ```java
      public static void main(String[] args) {
          Map<String,String> map = new HashMap<>();
          ...
      	Set<Map.Entry<String, String>> entrys = map.entrySet();
          for(Map.Entry<String, String> entry : entrys) {
              String key = entry.getKey();
              String value = entry.getValue();
              System.out.println(key+"和"+value);
          }
      }
      ```
  
- HashMap
  - 概述
    - 无序
    - 底层是哈希表，1.8之后是 数组 + 链表 / 红黑树
- **存储自定义类型的值**
  
  -  **key 必须重写 hashCode 和 equals 方法**才能实现HashMap集合中没有重复元素
  
- LinkedHashMap
  - 概述
    - **有序**，链表存储元素添加顺序
    - 底层是哈希表，1.8之后是 数组+链表 / 红黑树 + 链表
  
- HashTable

  - 概述
    - jdk 1.0 出现， HashMap jdk 1.2 出现
    - 同步的，不需要考虑并发
    - **键值都不能为空**

#### 线程

- 概述

  - 并发与并行
    - 并发：交替执行多个任务
    - 并行： 同时执行多个任务
  - 进程与线程
    - 进程：程序存储在硬盘中，运行时在内存中画一块区域运。内存中删除，则结束进程
    - 线程：进程中的执行单元，负责程序的执行，一个进程至少有一个线程
  - 线程的调度
    - 分时调度：固定时间段，轮流使用cpu
    - 抢占式调度：按照优先级高低排序使用，Java使用的是抢占式调度
  - 单线程与多线程
    - 单线程： 只有一个 main 方法所在的主线程，某一步出错，其后续都不执行
    - 多线程：除了主线程，还有多个自己创建的线程，某一部出错，不影响其他线程执行。不同线程的方法在不同的栈空间中，所以互不影响
  - 线程状态
    - ​    ![](G:\google下载\未命名文件.png)

- 实现途径

  - **创建 Thread 子类**

    - 创建一个Thread类的子类，重写run方法，方法体为要执行任务；创建子类对象，调用其 start 方法，开启新线程，执行 run 方法。

    - main线程和新创建的线程并发执行。线程只能启动一次，多次启动一个线程是非法地

    - ```java
      public static void main(String[] args) {
          MyThread mt = new MyThread();
          mt.start();
      }
      
      public class MyThread extends Thread{
          public void run() {
              for(int i=0; i<20; i++) {
                  System.out.println("thread"+i);
              }
          }
      }
      ```

  - **创建声明 Runnable 接口的类**

    - Runnable 接口应该由那些打算通过某一个线程执行其实例的类来实现。类必须定义一个成为 run 的无参方法

    - 优点

      - 继承了Thread类之后，便不能继承其他类，声明接口还可以继承其他类
      - 增强程序扩展性：设置线程和启动线程动作分离

    - ```java
      public static void main(String[] args) {
          RunnableImp ri = new RunnableImp();
          Thread t = new Thread(ri); 
          t.start();
      }
      
      public class RunnableImp implements Runnable{
          public void run() {
              for(int i=0; i<20; i++) {
                  System.out.println("thread"+i);
              }
          }
      }
      ```

    - 

- 构造方法

  - Thread(Runnable target)：分配新的 Thread 对象
  - Thread(Runnable target, String name)：分配新的 Thread 对象

- 常用方法

  - public **String getName**()：返回该线程的名称
  - public **static Thread currentThread()**：返回对当前正在执行的线程对象的引用
  - public **void setName()**：设置线程名称
  - public **static void sleep(long millis)**：使当前正在执行线程以指定毫秒数暂时停止运行

- 使用过程

  - getName 和 currentThread

    - ```java
      public static void main(String[] args) {
          MyThread mt = new MyThread();
          mt.start(); // Thread-0, Thread[Thread-0,main]
          Thread.currentThread().getName();  //main
      }
      
      public class MyThread extends Thread{
          public void run() {
      		String threadName = getName();
              Thread t = Thread.currentThread();
              System.out.println(threadName);
              System.out.println(t);
          }
      }
      ```

  - setName

    - 使用Thread类中的setName方法

      - ```java
        public static void main(String[] args) {
            MyThread mt = new MyThread();
            mt.setName("线程1");
            mt.start(); 	// 线程1
        }
        
        public class MyThread extends Thread{
            public void run() {
                String threadName = getName();
                System.out.println(threadName);
            }
        }
        ```

    - 创建一个**带参构造方法**，参数传递线程的名称；调用父类的带参构造方法，把线程名称传递给父类，让父类（Thread）给子线程起命名。

      - ```java
        public static void main(String[] args) {
            MyThread mt = new MyThread("线程2");
            mt.start();   //线程2
        }
        
        public class MyThread extends Thread{
            
            public MyThread() {
                super();
            }
        
        	public MyThread(String name) {
        		super(name);
        	}
        
            public void run() {
                String threadName = getName();
                System.out.println(threadName);
            }
        }
        ```

  - sleep

    - ```java
      public static void main(String[] args) {
          for(int i=0; i<20; i++) {
              System.out.println(i);
              try {
                  Thread.sleep(1000);   //每隔一秒输出一位
              } catch (InterruptedException e) {
                  e.printStackTrace();
              }
          }
      }
      ```

- 使用匿名内部类实现线程的创建

  - ```java
    public static void main(String[] args) {    //用thread类实现
        new Thread() {
            public void run() {
                for(int i=0; i<20;i++) {
                    System.out.println(i);
                }
            }
        }.start();
    }
    ```

  - ```java
    	public static void main(String[] args) {	//用Runnable接口实现
      		new Thread(new Runnable() {
      			public void run() {
      				for(int i=0; i<20; i++) {
      					System.out.println(i);
      				}
      			}
      		}).start();
      	}
    ```

- 线程同步

  - 同步代码块

    - 工作原理：多个线程调用cpu时，若某个线程正在调用cpu时，其他线程也想调用cpu，那么其他线程应进入阻塞状态。要实现这种安排，那么需要一个锁对象，该对象对所有线程必须是唯一的，当某个线程调用cpu时也占用该锁对象，其他线程因为无权占用锁对象，所以不能执行，只能等待锁对象被释放。
  
    - ```java
      synchronized(锁对象){   //锁对象对于所有线程必须是唯一的
          访问共享资源的代码
    }
      ```
  
    - ```java
      public static void main(String[] args) {
          Runnable run = new RunnableImpl();
          Thread t1 = new Thread(run);
          Thread t2 = new Thread(run);
          Thread t3 = new Thread(run);
      
          t1.start();
          t2.start();
          t3.start();
    }
      ```
      
    - ```java
      public class RunnableImpl implements Runnable{
      	private int tickets = 100;
      	Object obj = new Object();   //锁对象
       	public void run() {
        	while(true) {
        		synchronized(obj){	
                    if(tickets>0){
            			try {
        					Thread.sleep(100);	//提高多线程参与概率
        				} catch (InterruptedException e) {
        					// TODO 自动生成的 catch 块
        					e.printStackTrace();
        				}
                    	
                        System.out.println(Thread.currentThread().getName()+"正在卖第"+tickets+"张票");
                        tickets--;
                    }
                }
        	}
        }
      }
      ```
  
  - 同步方法
  
    - ```java
      public synchronized void payTicket(){
          ...
      }
      ```
  
    - ```java
      public static void main(String[] args) {
          Runnable run = new RunnableImpl();
          Thread t1 = new Thread(run);
          Thread t2 = new Thread(run);
          Thread t3 = new Thread(run);
      
          t1.start();
          t2.start();
          t3.start();
      
      }
      
      public class RunnableImpl implements Runnable{
      	private int tickets = 100;
          
          public void run() {
          	while(tickets>0) {
          		payTickets();
          	}
          }
          
          public synchronized void payTickets() {
          	if(tickets>0){
      			try {
      				Thread.sleep(100);	//提高多线程参与概率
      			} catch (InterruptedException e) {
      				// TODO 自动生成的 catch 块
      				e.printStackTrace();
      			}
              	
                  System.out.println(Thread.currentThread().getName()+"正在卖第"+tickets+"张票");
                  tickets--;
              }
          }
      }
      ```
  
    - 静态同步方法
  
      - payTickets 方法用 static 替换 synchronized
      - 锁对象不能用this，可以用反射中 RunnableImpl.getClass() 方法获取当前对象
  
  - Lock锁机制
  
    - 使用Lock 接口能清晰知道锁的运行状态和作用域
  
    - Lock 接口下有两个方法
  
      - public void **lock**()：获取锁， 
      - public void **unlock**()：释放锁
      - 用 lock 和 unlock 方法包围访问共享资源的代码
  
    - Lock 接口的**实现类 ReentrantLock** 调用lock 和 unlock 两个方法
  
    - ```java
      public class RunnableImpl implements Runnable{
      	private int tickets = 100;
          Lock l = new ReentrantLock();
          
          public void run() {
          	while(tickets>0) {
          		payTickets();
          	}
          }
          
          public void payTickets() {
              l.lock();		//起始位置
          	if(tickets>0){
      			try {
      				Thread.sleep(100);	//提高多线程参与概率
                      System.out.println(Thread.currentThread().getName()+"正在卖第"+tickets+"张票");
                  tickets--;
      			} catch (InterruptedException e) {
      				e.printStackTrace();
      			}finally{
                      l.unlock();   //终止位置, 而且放在finally中，无论是否出现异常都会释放锁
                  }
              }      
          }
      }
      ```
  
- 线程通信

  - 对于同一个资源，不同线程的任务不一样，例如，一个吃包子另一个做包子，应该做一个再吃一个。

  - 解决方案：等待唤醒机制

    - 没有包子-->顾客线程唤醒包子铺线程-->顾客线程等待-->包子铺线程做包子-->做好包子，修改包子状态为 有
    - 有包子-->包子铺线程唤醒顾客线程-->包子铺线程等待--->顾客吃包子-->包子吃完，修改包子状态为 无

  - 常用方法

    - wait：线程不再竞争cpu使用权，进入wait set
    - notify：唤醒所选取对象在wait set 中的一个线程进入等待队列
    - notifyAll：释放所选取对象在wait set 中所有线程

  - 注意事项

    - wait 方法和 notify 方法必须用**同一个锁对象调用**，因为，对应的锁对象可以通过 notify 唤醒使用同一个锁对象调用的 wait 方法后的线程
    - wait 方法与notify 方法属于**Object类**
    - wait 方法和 notiry 方法必须要在**同步代码块**或者**同步函数**中使用，因为必须要通过锁对象调用这两个方法
    - 为了避免虚假唤醒问题，wait方法务必用在while循环中。

  - 实践：包子铺和顾客

  - ```java
    public class Test2 {//包子铺线程做包子，顾客线程吃包子这两个线程互斥，做一个吃一个
    
    	public static void main(String[] args) {
    		// TODO 自动生成的方法存根
    		baozi bz = new baozi();
    		new baozipu(bz).start();
    		new guke(bz).start();
    	}
    
    }
    
    public class baozi {
    	String xian;
    	String pi;
    	boolean flag=false;
    }
    
    public class baozipu extends Thread{
    	private baozi bz;
        
    	public baozipu(baozi bz) {
    		super();
    		this.bz = bz;
    	}
    
    	public void run() {
    		int count = 0;
    		while(true) {
    			synchronized(bz) {
    				if(bz.flag == true) {
    					try {
    						bz.wait();
    					} catch (InterruptedException e) {
    						// TODO 自动生成的 catch 块
    						e.printStackTrace();
    					}
    				}
    				
    				if(count%2 ==0) {
    					bz.pi = "薄皮";
    					bz.xian = "韭菜肉";
    					
    				}else if(count%2 ==1) {
    					bz.pi ="厚皮";
    					bz.xian = "大葱肉";
    				}
    				System.out.println("包子铺正在做"+bz.pi+bz.xian+"的包子");
    				count++;
    				bz.flag = true;
    				bz.notify();
    				System.out.println("包子铺包子做了"+bz.pi+bz.xian+"包子");
    			}
    		}
    	}
    }
    
    public class guke extends Thread{
    	baozi bz = new baozi();
    	
    		public guke(baozi bz) {
    		super();
    		this.bz = bz;
    	}
    
    	public void run() {
    		while(true) {
    			synchronized(bz) {
    				if(bz.flag == false) {
    					try {
    						bz.wait();
    					} catch (InterruptedException e) {
    						// TODO 自动生成的 catch 块
    						e.printStackTrace();
    					}
    				}
    				
    				System.out.println("顾客正在吃"+bz.pi+bz.xian+"包子");
    				bz.flag = false;
    				bz.notify();
    				System.out.println("包子吃完了");
    				System.out.println("------------------");
    			}
    			
    		}
    	}
    }
    ```

- 线程池

  - 概述

    - 手动创建单个线程时花费大，线程池内的线程可重复使用，省去频繁创建线程操作，节约资源。
    - Executors 是线程池的工厂类，用来生成线程池

  - 常用方法

    - public **static ExecutorService newFixedThreadPool(int nThreads)**：创建一个可重用固定线程数的线程池
      - 返回值是**ExecutorService  接口**的实现类对象
    - public void **submit(Runnable task)**：提交一个Runnable 任务用来执行
    - public void **shutdown**()：关闭或销毁线程池方法，**不推荐使用**
    - submit 和 shutdown 都是ExecutorService接口中的方法

  - 使用过程

    - ```java
      public class Test3 {
      	public static void main(String[] args) {
      		ExecutorService es = Executors.newFixedThreadPool(3);
      		es.submit(new RunnableImpl2());//线程池调用线程执行接口中的run方法
      		es.submit(new RunnableImpl2());
      		es.submit(new RunnableImpl2());
      		es.submit(new RunnableImpl2());
      		
      		es.shutdown();   //线程池关闭，不推荐使用
      	}
      }
      
      public class RunnableImpl2 implements Runnable{
      	public void run() {
      		System.out.println(Thread.currentThread().getName()+"正在运行");
      	}
      }
      ```

#### Lambda表达式

- 概述

  - 解决**匿名内部类**代码书写繁琐问题
  - 面向对象编程强调谁来做，不关心怎么做，所以不得不创建一个对象。比如，使用Runnable匿名内部类开启线程时
  - 面向过程编程强调怎么做，不关心谁来做，只需要一段代码。
  - 综上两点，在Java中引入Lambda表达式，可以只传递一段代码，不需要创建对象
  - 方法的参数是接口时，可以不用创建匿名内部类，而是用Lambda表达式实现
  - 延迟执行，适用某些场景的代码执行后结果不一定被使用

- 格式

  - ```java
    (参数)->{代码}
    代码需要的参数放在括号中
    	(first,second)->first.length()-second.length();	//参数类型可以不写，只有一条代码时可以不写大括号
  	event->System.out.println(event);		//参数只有一个时，可以不写小括号
    	()->{for(int i=0; i<20; i++) System.out.println(i);}	//无参时，括号不能省略
    ```
    
  - ```java
    public static void main(String[] args) {	//用Lambda方法实现Runnable匿名内部类的作用
        new Thread(()->{
                for(int i=0; i<20; i++) {
                    System.out.println(i);
                }
            }
        }).start();
    }
    ```

- 练习

  - 无参无返回值练习

  - ```java
    public static void main(String[] args) {	//用Lambda方法实现Runnable匿名内部类的作用
    	invokeCook(()->{
            System.out.println("饭做好了")；
        });
    }
    public static void invokeCook(Cook cook){    //invokeCook 的方法参数是接口类型
       
    }
    ```

  - 有参有返回值练习

  - ```java
    public static void main(String[] args) {
    		ArrayList<Integer> list = new ArrayList<>();
    		Collections.addAll(list, 1,3,2,5,4);
    		
    		Collections.sort(list, (Integer o1, Integer o2)->{
    			return o2-o1;
    		}); //第二个参数本来是Comparator
    		System.out.println(list);  //5,4,3,2,1
    	}
    ```

  - 有参有返回值（自定义接口）

  - ```java
    public class Test3 {
        public static void main(String[] args) {
        calc(100,20,new Test3().new caculatorImpl()); //用自定义接口实现
            calc(10,20,(int a, int b)->{
                return a+b;
            });	//用lambda表达式实现
    
        }
        
        public static void calc(int a, int b, Caculator cac) {
            int sum = cac.sum(a,b);
            System.out.println(sum);
        }
        
        public class caculatorImpl implements Caculator{ 	//内部类
            @Override
            public int sum(int a, int b) {
                return a+b;
            }
        }
    ```
    

#### 函数式编程

- 函数式接口

  - 概述
    - 只包含**一个抽象方法**的接口，但也可以包含默认的，私有的或静态方法的方法
    - 语法糖
      - 原理不变，但使用更加方便的语法，如for-each循环底层是迭代器实现的
  - **FuntionalInterface** 注解
    - 在接口上添加，检测该接口是否为函数式

- 函数式编程

  - 概述

    - 当方法的**参数是函数式接口**时，可以用lambda表达式替换匿名内部类实现

  - 练习：优化日志案例

    - ```java
      public static void main(String[] args) {
          // TODO 自动生成的方法存根
          String str1 = "hello";
          String str2 = "world";
          String str3 = "Java";
      
          method(1,()->{		//用lambda实现，若level为1会拼接字符串
      			return str1+str2+str3;
      	});
      
          method(2,()->{		//用lambda实现，若level为2那么字符串不会拼接
      			return str1+str2+str3;
      	});
          
          method(1,new FunInterface() {		//用匿名内部类实现
              public String stringBuilder() {
                  System.out.println(str1+str2+str3);
                  return null;
              }
          });
      }
      
      public static void method(int level, FunInterface funint) {
          if(level == 1) {
              System.out.println(funint.stringBuilder());
          }
      }
      
      @FunctionalInterface
      public interface FunInterface {
      	public abstract String stringBuilder();
      }
      ```

    - 练习：用lambda表达式替换Runnable接口（函数式接口做方法参数）

      - ```java
        public static void main(String[] args) {
            // TODO 自动生成的方法存根
            startThread(new Runnable() {
                public void run() {
                    System.out.println(Thread.currentThread().getName()+"正在执行");
                }
            });
        
            startThread(()->{
                System.out.println(Thread.currentThread().getName()+"正在执行");
            });
        }
        
        public static void startThread(Runnable run) {
            new Thread(run).start();
        }
        //Thread-0正在执行； Thread-1正在执行
        ```

    - 练习：用lambda表达式替换Comparator接口（用函数式接口做返回值）

      - ```java
        public static void main(String[] args) {
            // TODO 自动生成的方法存根
            String[] strs = {"a","bb","ccc","dddd"};
            Arrays.sort(strs, getComparator());
            System.out.println(Arrays.toString(strs));//dddd,ccc,bb,a
            
            Arrays.sort(strs, new Comparator<String>() {
                public int compare(String str1, String str2) {
                    return str1.length() - str2.length();
                }
            });
            System.out.println(Arrays.toString(strs));//a,bb,ccc,dddd
        }
        
        public static Comparator<String> getComparator(){
            return (String str1, String str2)->{
                return str2.length() - str1.length();
            };
        }
        ```

- 常用的函数式接口

  - **Suppplier<T>** 接口中只有一个**T get()** 方法，泛型T是什么类型的，那 么get就返回什么类型

  - ```java
    public static void main(String[] args) {
        String s = getString(()->{
            return "123";
        });
        System.out.println(s);
    }
    
    public static String getString(Supplier<String> sup) {
        return sup.get();
    }
    ```

  - ```java
    public static void main(String[] args) {
        int[] arrs = {1,24,45,234,545,33};
        int max = getMax(()->{
            int maxValue = arrs[0];
            for(int i: arrs) {
                if(i>maxValue)
                    maxValue = i;
            }
            return maxValue;
        });
        System.out.println(max);	//545
    }
    
    public static Integer getMax(Supplier<Integer> sup) {
        return sup.get();
    }
    ```

  - **Consumer<T>** 接口有一个 抽象方法 **void accept(T t)** ，该接口用来消费数据，对传进来的参数进行操作

  - ```java
    public static void main(String[] args) {
        String str = "人在家中坐";
        consumerInter(str,(String restr)->{
            String newStr = new StringBuffer(restr).reverse().toString();
            System.out.println(newStr);
        });
    }
    
    public static void consumerInter(String str, Consumer<String> con) {
        con.accept(str);
    }
    ```

  - 默认方法  **Consumer<T> andThen (Consumer<?  super T> after)**

    - 两个Consumer接口，把两个接口组合在一起使用

    - ```java
      public static void main(String[] args) {
          method("abcd",
          (t)->{	//对t进行两次操作
              System.out.println(t.toUpperCase()); //ABCD
          },
          (t)->{
              System.out.println(t+"e");	//abcde
          });
      }
      
      public static void method(String str, Consumer<String> con1, Consumer<String> con2) {
          con1.andThen(con2).accept(str);
      }
      ```

    - ```java
      public static void main(String[] args) {
          // 
          String[] strings = {"张三，18","李四，20"};
          method(strings, 
          (t)->{
              String name = t.split(",")[0];
              System.out.print("姓名： " + name);
          },
          (t)->{
               String age = t.split(",")[1];
               System.out.println("年龄： "+ age);
           });
          //姓名：张三年龄：18  ...
      }
      
      public static void method(String[] strs, Consumer<String> con1, Consumer<String> con2) {
          for(String str : strs) {
              con1.andThen(con2).accept(str);
          }
      }
      ```

  - **Predicate<T> 接口**：对某中数据类型的属性进行判断，返回一个boolean 值。 
  
    - **抽象方法 boolean test(T t)**：用来对指定类型数据进行判断
  
    - ```java
      public static void main(String[] args) {
          int i = 10;
          boolean bol = method(i,(Integer t)->{
              return t>2;
          });
          System.out.println(bol);
      }
      
      public static boolean method(int i, Predicate<Integer> pre) {
          return pre.test(i);		// true
          //return pre.negate().test(i);  false
      	//默认方法 negate() 用来对结果取反，等于 return !pre.test(i);
      }
      ```
  
    - 默认方法 and与 ，or或
  
    - ```java
      public static void main(String[] args) {
          int i = 10;
          boolean bol = method(i,(Integer t)->{
              return t>2;
          },(Integer t)->{
              return t<9;
          });
          System.out.println(bol);
      }
      
      public static boolean method(int i, Predicate<Integer> pre1, Predicate<Integer> pre2) {
          //return pre1.test(i) && pre2.test(i);	false	
          //return pre1.and(pre2).test(i);		false
          
          //return pre1.test(i) || pre2.test(i);	true
          //return pre1.or(pre2).test(i);			true
          
          return pre1.test(i) != pre2.test(i);	//判断两个接口的返回值是否不相等, true
      
      }
      ```
  
    - 练习：输出名字是四位，性别为女的人
  
    - ```java
      public static void main(String[] args) {
          // TODO 自动生成的方法存根
          String[] strs = {"古力娜扎，女","地里热吧，女","蔡徐坤，男","董洁，女"};
          for(String str : strs) {
              boolean bol = method(str, 
              (String string)->{
                  String name = string.split("，")[0];
                  return name.length() == 4;
               },
               (String string)->{
                   String sex = string.split("，")[1];
                   return sex.equals("女");
               });
              if(bol == true) {
                  System.out.println(str); //只有前两个
              }
          }
      }
      
      public static boolean method(String str, Predicate<String> pre1, Predicate<String> pre2) {
          return pre1.and(pre2).test(str);
      }
      ```
  
  - **Function<T, R> 接口**用来根据一个类型的数据获得另一个类型的数据，T称为前置条件，R称为后置条件
  
  - **抽象方法：R apply(T t)** ：将T类型数据转换为R类型数据
  
  - ```java
    public static void main(String[] args) {
        String str = "1234";
        Integer i = changeType(str,(String string)->{
            return Integer.parseInt(string);
        });
        System.out.println(i);	//1234
    }
    
    public static Integer changeType(String string, Function<String,Integer> fun) {
        return fun.apply(string);
    }
    ```
  
  - 练习：将String转为Integer并加10，然后再转为String并输出
  
  - ```java
    public static void main(String[] args) {
        String str = "1234";
        String string = changeType(str,
        (String str1)->{
             Integer i = Integer.parseInt(str1);
             return i;
         },
         (Integer i1)->{
          	 i1 += 10;
             return i1.toString();
         });
        System.out.println(string); //1244
    }
    
    public static String changeType(String string, Function<String,Integer> fun1,Function<Integer,String> fun2) {
        return fun2.apply(fun1.apply(string));
        //return fun1.andThen(fun2).apply(string);
    }
    ```
  
  - 

#### 7.容器 

#### 8.反射 ：框架设计的灵魂

![](G:\BaiduNetdiskDownload\反射注解笔记\笔记\Java代码的三个阶段.bmp)

	* 框架：半成品软件。可以在框架的基础上进行软件开发，简化编码
	* 反射：将类的各个组成部分封装为其他对象，这就是反射机制
		* 好处：
			1. 可以在程序运行过程中，操作这些对象。
			2. 可以解耦，提高程序的可扩展性。


	* 获取Class对象的方式：
		1. Class.forName("全类名")：将字节码文件加载进内存，返回Class对象
			* 多用于配置文件，将类名定义在配置文件中。读取文件，加载类
		2. 类名.class：通过类名的属性class获取
			* 多用于参数的传递
		3. 对象.getClass()：getClass()方法在Object类中定义着。
			* 多用于对象的获取字节码的方式
	
		* 结论：
			同一个字节码文件(*.class)在一次程序运行过程中，只会被加载一次，不论通过哪一种方式获取的Class对象都是同一个。


	* Class对象功能：
		* 获取功能：
			1. 获取成员变量们
				* Field[] getFields() ：获取所有public修饰的成员变量
				* Field getField(String name)   获取指定名称的 public修饰的成员变量
	
				* Field[] getDeclaredFields()  获取所有的成员变量，不考虑修饰符
				* Field getDeclaredField(String name)  
			2. 获取构造方法们
				* Constructor<?>[] getConstructors()  
				* Constructor<T> getConstructor(类<?>... parameterTypes)  
	
				* Constructor<T> getDeclaredConstructor(类<?>... parameterTypes)  
				* Constructor<?>[] getDeclaredConstructors()  
			3. 获取成员方法们：
				* Method[] getMethods()  
				* Method getMethod(String name, 类<?>... parameterTypes)  
	
				* Method[] getDeclaredMethods()  
				* Method getDeclaredMethod(String name, 类<?>... parameterTypes)  
	
			4. 获取全类名	
				* String getName()  


	* Field：成员变量
		* 操作：
			1. 设置值
				* void set(Object obj, Object value)  
			2. 获取值
				* get(Object obj) 
	
			3. 忽略访问权限修饰符的安全检查
				* setAccessible(true):暴力反射



	* Constructor:构造方法
		* 创建对象：
			* T newInstance(Object... initargs)  
	
			* 如果使用空参数构造方法创建对象，操作可以简化：Class对象的newInstance方法


	* Method：方法对象
		* 执行方法：
			* Object invoke(Object obj, Object... args)  
	
		* 获取方法名称：
			* String getName:获取方法名


	* 案例：
		* 需求：写一个"框架"，不能改变该类的任何代码的前提下，可以帮我们创建任意类的对象，并且执行其中任意方法
			* 实现：
				1. 配置文件
				2. 反射
			* 步骤：
				1. 将需要创建的对象的全类名和需要执行的方法定义在配置文件中
				2. 在程序中加载读取配置文件
				3. 使用反射技术来加载类文件进内存
				4. 创建对象
				5. 执行方法



#### 9.注解 

* 概念：说明程序的。给计算机看的

* 注释：用文字描述程序的。给程序员看的

* 定义：注解（Annotation），也叫元数据。一种代码级别的说明。它是JDK1.5及以后版本引入的一个特性，与类、接口、枚举是在同一个层次。它可以声明在包、类、字段、方法、局部变量、方法参数等的前面，用来对这些元素进行说明，注释。

* 概念描述：
	* JDK1.5之后的新特性
	* 说明程序的
	* 使用注解：@注解名称
	
* 作用分类：
  ①编写文档：通过代码里标识的注解生成文档【生成文档doc文档】
  	②代码分析：通过代码里标识的注解对代码进行分析【使用反射】
  	③编译检查：通过代码里标识的注解让编译器能够实现基本的编译检查【Override】

* JDK中预定义的一些注解

  * @Override	：检测被该注解标注的方法是否是继承自父类(接口)的
  * @Deprecated：该注解标注的内容，表示已过时
  * @SuppressWarnings：压制警告
  	* 一般传递参数all  @SuppressWarnings("all")

* 自定义注解
	* 格式：
		元注解
		public @interface 注解名称{
			属性列表;
		}

	* 本质：注解本质上就是一个接口，该接口默认继承Annotation接口
		* public interface MyAnno extends java.lang.annotation.Annotation {}

	* 属性：接口中的抽象方法
		* 要求：
			1. 属性的返回值类型有下列取值
				* 基本数据类型
				* String
				* 枚举
				* 注解
				* 以上类型的数组

			2. 定义了属性，在使用时需要给属性赋值
				1. 如果定义属性时，使用default关键字给属性默认初始化值，则使用注解时，可以不进行属性的赋值。
				2. 如果只有一个属性需要赋值，并且属性的名称是value，则value可以省略，直接定义值即可。
				3. 数组赋值时，值使用{}包裹。如果数组中只有一个值，则{}可以省略
	
	* 元注解：用于描述注解的注解
		* @Target：描述注解能够作用的位置
			* ElementType取值：
				* TYPE：可以作用于类，接口，枚举上
				* METHOD：可以作用于方法上
				* FIELD：可以作用于成员变量上
		* @Retention：描述注解被保留的阶段
			* @Retention(RetentionPolicy.RUNTIME)：当前被描述的注解，会保留到class字节码文件中，并被JVM读取到
		* @Documented：描述注解是否被抽取到api文档中
		* @Inherited：描述注解是否被子类继承
	
* 在程序使用(解析)注解：获取注解中定义的属性值
  	1. 获取注解定义的位置的对象  （Class，Method,Field）
    	2. 获取指定的注解
    		* getAnnotation(Class)
               		//其实就是在内存中生成了一个该注解接口的子类实现对象

    	            public class ProImpl implements Pro{
    	                public String className(){
    	                    return "cn.itcast.annotation.Demo1";
    	                }
    	                public String methodName(){
    	                    return "show";
    	                }
    	            }
  
    	3. 调用注解中的抽象方法获取配置的属性值

#### 单元测试

![](G:\BaiduNetdiskDownload\反射注解笔记\笔记\测试分类.bmp)

 测试分类：
	1. 黑盒测试：不需要写代码，给输入值，看程序是否能够输出期望的值。
	2. 白盒测试：需要写代码的。关注程序具体的执行流程。

* Junit使用：白盒测试
	* 步骤：
		1. 定义一个测试类(测试用例)
			* 建议：
				* 测试类名：被测试的类名Test		CalculatorTest
				* 包名：xxx.xxx.xx.test		cn.itcast.test

		2. 定义测试方法：可以独立运行
			* 建议：
				* 方法名：test测试的方法名		testAdd()  
				* 返回值：void
				* 参数列表：空参

		3. 给方法加@Test
		4. 导入junit依赖环境

	* 判定结果：
		* 红色：失败
		* 绿色：成功
		* 一般我们会使用断言操作来处理结果
			* Assert.assertEquals(期望的结果,运算的结果);

	* 补充：
		* @Before:
			* 修饰的方法会在测试方法之前被自动执行
		* @After:
			* 修饰的方法会在测试方法执行之后自动被执行

#### 10.IO流 

- 概述

  - 内存<--->磁盘之间数据传输，一切数据都为字节

  - |        | 输入流      | 输出流       |
    | ------ | ----------- | ------------ |
    | 字节流 | InputStream | OutputStream |
    | 字符流 | Reader      | Writer       |

  - 一个字符 == 两个字节 

- **字节流**

  - **OutputStream**

    - 概述

      - 一个**抽象类**，字节输出流，向磁盘中写

    - 抽象方法

      - public void **close**()：关闭此输出流，并释放相关资源，不然会占用资源
      - public void **flush**()：刷新此输出流并强制输出已缓冲字节
      - public abstract void **write(int b)**：指定的字节写入此输出流，ascll码值对应的字符
      - public void **write(byte[] b)**：将b.length个字节从指定的byte数组写入此输出流
        - 若字符为**负数**，那么第一个和第二个会组合在一起翻译成**中文**
        - 也可以用String类的**getBytes** 方法，将**字符转化成字节**，写入指定文件中
      - public void **write(byte[] b, int off, int len)**：将指定byte数组中从偏移量off开始的len个字节写入此输出流，左闭右开。
      - 

    - 使用过程

      - ```java
        public static void main(String[] args) throws IOException {
            FileOutputStream fos = new 			         			FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\b.txt");
            fos.write(100);	
            byte[] bytes = {-65,-66,67,68,69}; 
            fos.write(bytes);
            fos.write(bytes, 0, 2);
            
            byte[] bytes1 = "字符".getBytes();
        	System.out.println(Arrays.toString(bytes1));
            //[-41, -42, -73, -5]，gbk编码是两个字节对应一个汉字，utf-8是三个字节对应一个汉字
        	fos.write(bytes1);
            
            fos.close();
        }
        //b.txt文本内容为 d烤CDE烤字符
        ```

    - **FileOutputStream** extends OutputStream

    - 概述

      - 文件字节输出流，将内存中数据写入到磁盘中

    - 构造方法

      - **FileOutputStream** (**String name**)：创建一个向具有制定名称的文件中写入数据的输出文件流，新建文件并写入数据
      - FileOutputStream (**File file**)：构建一个向指定File 对象写入数据的文件输出流
      - FileOutputStream(**String name, boolean append**)：创建一个向指定name的文件中写入数据的文件输出流
        - append 为 true，向原有文件续写；反之覆盖原有文件
      - FileOutputStream(**File file, boolean append**)：创建一个向File对象表示的文件中写入数据的文件输出流

    - 构造方法使用过程

      - ```java
        public static void main(String[] args) throws IOException{	
            FileOutputStream fos = new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\c.txt",true);
            //c.txt 每一次写入都会续写
            fos.write("你好".getBytes());
            fos.write("\r\n".getBytes());
            for(int i=0; i<3;i++) {
                fos.write("世界".getBytes());
                fos.write("\r\n".getBytes()); //wins 是"\r\n"， linux 是 "/n"
            }
        
            fos.close();
        }//c.txt 内容为  你好（换行）世界（换行）世界（换行）世界
        ```

  - **InputStream**

    - 概述

      - 一个抽象类，字节输入流，从磁盘中读，

    - 抽象方法

      - public **int read**()：从输入流中读取数据的下一个字节
        - 读取完所有数据后，返回 **-1**
      - public **int read(byte[] b)**：从输入流中读取一定数量的字节（由b的长度决定），并将其存储在**缓冲区数组b**中，返回b 的长度
      - public void **close()**：关闭此输入流并释放与该流关联的系统资源

    - 抽象方法使用过程

      - ```java
        public static void main(String[] args) throws IOException{	
            FileInputStream fis = new FileInputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt"); 
            //a.txt 内容是da
            int a = fis.read();		//单个字节读取
            System.out.println(a);  //100， d的ascll值
            System.out.println((char)a);  //d
            
            byte[] bytes = new byte[2];		//按字节数组长度读取字节个数
            int len = fis.read(bytes);
            System.out.println(len);	//2
            System.out.println(Arrays.toString(bytes));	//[100, 97]
            System.out.println(new String(bytes));	//da
            
            int len1 = fis.read(bytes);		//字节都读取完之后的情况
            System.out.println(len1);		//-1
            System.out.println(Arrays.toString(bytes));	//[100, 97]
            System.out.println(new String(bytes));	//da
            //字节数组 b 存储的是上一次读取到的数据，读取到新的数据才会覆盖原有的
            
            //简化一次读取多个数据的步骤
            byte[] bytes1 = new byte[1024];	//一般设置为1024的倍数
            int len = 0;
            while((len = fis.read(bytes1)) != -1) {
                System.out.println(new String(bytes1, 0, len)); //根据字节数组长度打印
            }
            
            fis.close();
        }
        ```

    - **FileInputStream** extends InputStream

      - 概述
        - 把硬盘中文件的数据，读取到内存中使用
      - 构造方法
        - FileInputStream(**String name**)：创建name指定路径的文件的输入流
        - FileInputStream(**File file**)：创建一个file指定对象的文件的输入流

    - 输入输出练习：文件复制

      - ```java
        public static void main(String[] args) throws IOException{	
            FileInputStream fis = new FileInputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt");
            FileOutputStream fos = new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\新建文件夹\\a.txt");
            //输出流路径要写复制后文件的名字
            byte[] bytes = new byte[1024];
            int len = 0;
            while((len = fis.read(bytes)) != -1) {
                fos.write(bytes,0,len);
            }
            fos.close();	//先关输出流，再关输入流
            fis.close();
        }
        ```

- **字符流**

  - **Reader**

    - 概述

      - 一个抽象类，用来读取字符流
      - 用字节流时，想要获取中文时可能会出错

    - 抽象方法

      - public int read()：读取单个字符并返回
      - public int read(char[] cbuf)：一次读取多个字符将其写入字符数组中
      - public void close()：关闭流，释放资源

    - **FileReader** extends InputStreamReader extends Reader

      - 概述

        - 把硬盘中文件以字符形式读取到内存中

      - 构造方法

        - **FileReader(String fileName)**：根据指定路径创建FileReader对象
        - **FileReader(File file)**：根据指定的File对象创建FileReader对象

      - 使用过程

        - ```java
          public static void main(String[] args) throws IOException{	
              FileReader fr = new FileReader("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt"); //a.txt内容abcd
          		int c1 = fr.read();
          		System.out.println((char)c1); //a
          		char[] c2 = new char[1024];
          		int len = 0;
          		while((len = fr.read(c2)) != -1) {
          			System.out.println(new String(c2,0,len)); //bcd
          		}
          }
          ```

  - **Writer**

    - 概述

      - 一个抽象类，用来输出字符流

    - 抽象方法

      - public void write()：写入单个字符
      - public void write(char[] cbuf)：写入字符数组
      - pulic abstract void write(char[] cbuf, int off, int len)：写入字符数组的某一部分，起始位置和长度
      - public void write(String str)：写入字符串
      - public void write(String str, int off, int len)：写入字符串某一部分，起始位置和长度
      - public void flush()：刷新缓冲区，刷新后字符才能写入到磁盘中
      - public void close()：关闭输出流，但要先刷新
        - flush 和 close 的区别
          - flush 刷新完，还可以继续写入数据
          - close 先刷新，后关闭输出流，之后不能继续写入数据了

    - **FileWriter** extends OutputStreamWriter extends Writer

      - 概述

        - 把内存中字符数据写入到**内存缓冲区**中，必须要**flush**后才会写入到文件中，这点和输出字节流不同，

      - 构造方法

        - FileWriter(File file)：根据指定的File对象创建输出字符流对象
        - FileWriter(String fileName)：根据指定路径创建输出字符流对象
        - FileWriter()(File file, **boolean append**)：是否续写
        - FileWriter(String fileName, **boolean append**)：是否续写

      - 方法使用过程

        - ```java
          public static void main(String[] args) throws IOException{	
              FileWriter fw = new FileWriter("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt");
              FileReader fr = new FileReader("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt");
              fw.write(97);		//覆盖了a.txt原有内容
              fw.flush();			//写完记得flush才能生效
              			
              int c = fr.read();
              System.out.println((char)c);  // 输出a
              
              char[] ch = {'a','b','c','d','e'};	//用字符数组写入
              fw.write(ch);			//数组内容全部写入
              fw.write("\r\n");
              fw.write(ch, 0, 3);		//只写前三个
              fw.write("\r\n");
          
              String str = "你好世界";	//用字符串写入
              fw.write(str+"\r\n");		//字符串内容全部写入
              fw.write(str, 2, 2);	//只写后两个
              fw.write("\r\n");
              fw.flush();				//写完记得刷新
          
              char[] ch1 = new char[1024];	//读数据
              int len = 0;
              while((len = fr.read(ch1)) != -1) {
                  System.out.println(new String(ch1,0,len));
                  //输出结果是 abcde（换行）abc（换行）你好世界（换行）世界
              }
              fr.close();			//关闭读数据流
              fw.close();			//关闭写数据流		
          }
          ```


#### Properties 集合

- 概述

  - Properties extends **HashTable**<k,v> implements **Map**<k,v>
  - 一个持久的属性集，可保存在在流中或从流中读取
    - Properties 集合中的**store**方法把集合中临时数据写到磁盘中，**往磁盘中写**
    - Properties 集合总**load** 方法把硬盘中的文件读取到集合中使用，**往集合中读**
  - 双列集合，key 和 value 默认都是字符串

- 常用方法

  - public Object **setProperty**(String key, String value)：往集合中添加键值对，调用 HashTable 的方法put
  - public **String getProperty**(String key)：通过key找value
  - public **Set<String> stringPropertyNames**()：返回此属性列表中的键集
  - public void **store**(**OutputStream out**, String comments)：往Properties集合中传字节流，**不能写中文的字节流**，会乱码
  - public void **store**(**Writer writer**, String comments)： 往Properties集合中传字符流，**可以写中文的字符流**
    - comments ：注释，一般为空，不能用中文
  - public void **load(InputStream inStream)**：从硬盘文件中读取键值对到集合中，**不能读取中文键值对**
  - public void **load(Reader reader)**，可以读取中文键值对

- 常用方法使用过程

  - ```java
    public static void main(String[] args) {	//set,get,StringPropertyNames
        Properties pro = new Properties();
        pro.setProperty("张三", "169");
        pro.setProperty("李四", "180");
        pro.setProperty("王五", "165");
    
        Set<String> keys = pro.stringPropertyNames();
        for(String key : keys) {
            String value = pro.getProperty(key);
            System.out.println(key+"="+value);	//张三=169 李四....
        }
    }
    ```

  - ```java
    public static void main(String[] args) { //用store通过字节和字符流往磁盘中写
        Properties pro = new Properties();
        pro.setProperty("张三", "169");
        pro.setProperty("李四", "180");
        pro.setProperty("王五", "165");
    
        FileOutputStream fis = new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\pro.txt");
        pro.store(fis, "save date");
        FileWriter fw = new FileWriter("C:\\Users\\xpty\\Desktop\\Javabasic\\pro1.txt");
        pro.store(fw, "save date");
        fw.close();
        fis.close();
    }
    ```

  - ```java
    public static void main(String[] args) throws IOException {//load
        Properties pro = new Properties();
        FileReader fr = new FileReader("C:\\Users\\xpty\\Desktop\\Javabasic\\pro.txt");
        pro.load(fr);
        Set<String> keys = pro.stringPropertyNames();
        for(String key : keys) {
            String value = pro.getProperty(key);
            System.out.println(key+"="+value);
        }
    }
    ```



#### 缓冲流，转换流，序列化流

- **缓冲流**

  - 概述

    - **IO流的增强**
    - 在创建流对象时，会创建一个内置的默认大小的缓冲区数组，每次IO操作以数组形式传递，提高效率

  - 成员方法

    - **缓冲字节流**方法基本继承自InputStream, OutputStream, Reader, Writer 
    - **缓冲字符流**
      - public **String readLine()**：读取一行字符，行终止条件：碰见换行（\n)，回车（\r），换行回车（\r\n）时认为该行结束，返回终止符以前的字符串； 文本遍历完后返回null。
      - public void **newLine**()：写入一个行分隔符

  - 构造方法

    - 缓冲字节流
      - **BufferedInputStream**(InputStream in)
      - BufferedInputStream(InputStream in, int size)：size 是手动指定缓冲区大小
      - **BufferedOutputStream**(OutputStream out)
      - BufferedOutputStream(OutputStream out, int size)：
    - 缓冲字符流
      - **BufferedReader**(Reader in)
      - BufferedReader(Reader in, int sz)
      - **BufferedWriter**(Writer out)
      - BufferedWriter(Writer out, int sz)

  - 方法使用过程

    - 缓冲字节流

      - ```java
        FileInputStream fis = new FileInputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt");
        BufferedInputStream bis = new BufferedInputStream(fr);
        //用bis，不用fis，操作步骤不变
        
        FileOutputStream fos = new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\b.txt");
        BufferedOutputStream bos = new BufferedOutputStream(fos);
        //用bos，不用fos，操作步骤不变
        ```

    - 缓冲字符流

      - ```java
        BufferedReader br = new BufferedReader(new FileReader("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt"));
        String line;
        while((line = br.readLine()) != null) { 
            System.out.println(line);
        }
        
        BufferedWriter bw = new BufferedWriter(new FileWriter("C:\\Users\\xpty\\Desktop\\Javabasic\\b.txt"));
        for(int i=0; i<3; i++) {
            bw.write("你好");
            bw.newLine();
        }
        bw.close();
        ```

- **转换流**

  - 概述

    - 采用何种编码和解码格式，取决于最终显示地方的格式。转换流不管流的来源是什么格式，只关心当前需要转换成什么格式。
    - 编码与解码
      - 编码： 人类语言-->机器语言
      - 解码：机器语言-->人类语言
    - 字符编码和字符集
      - ![](G:\google下载\未命名文件 (1).png)
      - ASCII：基于拉丁文，7位表示一个字符，共128个字符，8位表示负数
      - ISO-8859-1：基于欧洲使用语言，单字节编码，兼容ASCII编码
      - GBK：国标，基于中文，双字节编码，
        - Unicode：万国码，全球语言统一在一起；ASCII用一个字节，拉丁文用两个字节，中文大部分用三个字节，四个字节很少使用
    - Wins默认ANSI (GBK) 编码
    - 解决不同编码文件的转码需求

  - **InputStreamReader** extends Reader

    - 概述

      - 通过指定编码格式，将字节流--->字符流

    - 成员方法：继承父类

    - 构造方法

      - InputStreamReader(InputStream in)：创建一个默认字符集的InputStreamReader对象，eclipse默认gbk，IDE默认utf-8
      - **InputStreamReader(InputStream in, String charsetName)**：根据指定的字符集创建对象
        - charsetName：utf-8 / UTF-8, gbk/GBK

    - 方法使用过程

      - ```java
        InputStreamReader isr = new InputStreamReader(new FileInputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt"),"utf-8");
        //将a.txt中文件以utf-8形式创建InputStreamReader对象
        char[] chars = new char[1024];   //字节-->字符，所以此处用字符数组接收
        int len = 0;
        while((len = isr.read(chars)) != -1) {
            System.out.println(chars);
        }
        isr.close();	//以utf-8格式打开中文
        ```

  - **OutputStreamWriter** extends Writer

    - 概述

      - 通过指定解码格式，将字符流-->字节流

    - 成员方法：继承父类

    - 构造方法

      - OutputStreamWriter(OutputStream out)
      - OutputStreamWriter(OutputStream out, String charsetName)

    - 方法使用过程

      - ```java
        OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt"),"gbk");
        osw.write("你好啊");
        osw.close();
        ```

  - 将utf-8编码文件另存为gbk编码文件

    -  ```java
      InputStreamReader isr = new InputStreamReader(new FileInputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\utf-8编码.txt"));
      char[] chars = new char[1024];
      int len=0;
      while((len =isr.read(chars)) != -1) {
          System.out.println(new String(chars, 0, len));
      }
      OutputStreamWriter osw = new OutputStreamWriter(new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\gbk编码.txt"),"gbk");
      osw.write(chars);
      isr.close();
      osw.close();
      ```

- **打印流**

  - 概述

    - **PrintStream extends OutputStream**
    - PrintStream ：为其他输出流添加功能，是他们能够方便地打印**各种数据类型**

  - 成员方法：继承父类+特有方法

    - print：只打印
    - println：打印后换行
    - 注意
      - 若使用**继承自父类的writer 方法**写数据，那么查看数据的时候会**查询编码表** 97->a
      - 若使用自己**特有的方法print / println 方法**写数据，写的数据**原样输出** 97->97
      - **System.setOut(PrintStream out)**：将System的输出位置改为 out 所指位置

  - 构造方法

    - PrintStream(File file)：输出的目的地是一个文件
    - PrintStream(OutputStream out)：输出目的地是一个字节输出流
    - PrintStream(String fileName)：输出目的地是一个文件路径

  - 方法使用过程

    - ```java
      PrintStream ps = new PrintStream("C:\\Users\\xpty\\Desktop\\Javabasic\\a.txt");
      ps.write(97);		//写出来的是a
      ps.print(97);		//写出来的是97
      ps.print(true);		//写出来的是true
      
      System.out.println("控制台输出"); 	 //在控制台输出
      System.setOut(ps);				 	//改变输出位置
      System.out.println("a.txt中输出");	  //在a.txt中输出
      ps.close();
      ```

- 序列化流

  - 概述

    - **ObjectOutputStream** extends OutputStream
    - 把对象以流的方式写入到文件中保存
    - 用二进制格式，将字符从 内存-->硬盘
    - 序列化的对象一定要声明 **Serializable 接口**

  - 构造方法

    - **ObjectOutputStream(OutputStream out)**

  - 成员方法：继承+特有

    - public void writeObject(Object obj)：将指定的对象写入 ObjectOutputStream

  - 使用过程

    - ```java
      public static void main(String[] args) throws IOException {
          ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\b.txt"));
          oos.writeObject(new Person("张三",18));	//将person对象序列化写入到b.txt中
          oos.close();
      }
      
      public class Person implements Serializable{
          String name;
          int age;
          ...
      }
      ```

- 反序列化流

  - 概述
    - **ObjectInputStream** extends InputStream
    - 文件中保存的对象以流的方式写入到内存中使用
    - 硬盘-->内存
    
  - 构造方法
    
    - **ObjectInputStream(InputStream in)**
    
  - 成员方法：继承+特有
    
    - public **Object readObject(Object obj)**：从指定对象中写入
    
  - 使用过程、
  
    - ```java
      ObjectInputStream ois = new ObjectInputStream(new FileInputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\b.txt"));
      Object o = ois.readObject();
      System.out.println(o);
      ```
  
- **transient** 关键字

  - 概述
    - 瞬态关键字，被transient修饰的变量**不能被序列化**，就是说经过序列化和反序列化之后，被修饰的成员变量还是**默认值**
    - static 静态关键字 ，静态优先于非静态加载到内存中，被static修饰的成员变量不能被序列化

- InvalidClassException

  - 序列化和反序列化时都会生成一个该对象特有的ID叫做serialVersionID，当序列化对象被修改时该ID会改变，所以会抛出异常

  - 解决方法：在被序列化对象中手动声明serialVersionID，不再有系统自动生成

    - ```java
      private static final long serialVersionID = 1234L;
      ```

- 练习：将对象存储在集合中序列化

  - ```java
    public static void main(String[] args) throws IOException {
        ObjectOutputStream oos = new ObjectOutputStream(new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\b.txt"));
        ArrayList<Person> persons = new ArrayList<>();
        persons.add(new Person("c罗",30));
        persons.add(new Person("梅西",31));
        persons.add(new Person("大罗",32));
        persons.add(new Person("小罗",33));
        
        oos.writeObject(persons);
        oos.close();
    
        ObjectInputStream ois = new ObjectInputStream(new FileInputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\b.txt"));
        Object o = ois.readObject();
        ois.close();
        ArrayList<Person> arrays = (ArrayList<Person>)o;
        for(Person p : arrays) {
            System.out.println(p);
        }  
    }
    ```

- 练习：利用map集合对文本进行排序

  - ```java
    public static void main(String[] args) throws IOException {
        BufferedReader br = new BufferedReader(new FileReader("C:\\Users\\xpty\\Desktop\\Javabasic\\文本排序.txt"));
        BufferedWriter bw = new BufferedWriter(new FileWriter("C:\\Users\\xpty\\Desktop\\Javabasic\\文本排序结果.txt"));
        Map<String,String> map = new HashMap<>();
    
        String line;
        while((line = br.readLine()) != null) {
            String[] strs = line.split("\\.");
            map.put(strs[0], strs[1]);	//利用map集合对key自动排序的特性
        }
    
        for(String key: map.keySet()) {
            bw.write(key +"."+ map.get(key));
            bw.newLine();
        }
        br.close();
        bw.close();
    }
    ```

#### 网络编程

- 概述

  - 客户端和服务器之间通过**字节流**交互
  - 服务器自身不产生流，而是接收客户端的输出流，将信息写入该输出流，然后返回给客户端
  - 通过Socket类来实现套接字编程

- Socket

  - 概述
    - 此类实现**客户端**套接字
    - 此类实现两台机器间的通信，套接字是两台机器间通信的端点，套接字是包含了IP地址和端口号的网络单位
  - 构造方法
    - Socket(String **host**, int **por**t)：创建一个流套接字并将其连接到**指定主机**的**指定端口**号
  - 成员方法
    - public OutputStream **getOutputStream**()
    - public InputStream **getInputStream**()
    - public void close()

- ServerSocket

  - 概述
    - 此类实现**服务端**套接字
    - 接受客户端请求，读取客户端发送的数据，给客户端写数据
  - 构造方法
    - **ServerSocket(int port)**：创建绑定特定端口服务器的套接字
  - 成员方法
    - Public **Socket accept**()：侦听并接受到此套接字的连接

- 注意：文件上传阻塞问题

  - 当len没有接收到-1时该循环一直存在，后序代码永远不能执行，所以文件上传完毕后给服务器一个结束标记，要调用**shutdow**方法手动关闭套接字的输出流

  - ```java
    //经常用的while循环搭配read方法可能会阻塞的解决方案
    int len =0;
    byte[] bytes = new byte[1024];
    while((len = fis.read(bytes)) != -1){
        os.write(bytes,0,len);
    }
    socket.shutdownOutput();	//已经接受的数据加上TCP尾部正常发送
    ...
    ```

- 练习：客户端和服务端发送信息

  - ```java
    //客户端
    public static void main(String[] args) throws IOException {
        Socket s = new Socket("127.0.0.1",8080);
        InputStream is = s.getInputStream();
        OutputStream os = s.getOutputStream();
        byte[] bytes = new byte[1024];
    
        os.write("你好服务端".getBytes());
        os.flush();
    
        int len = is.read(bytes);
        System.out.println(new String(bytes, 0, len));
    
        s.close();
    }
    ```

  - ```java
    //服务端
    public static void main(String[] args) throws IOException {
        // TODO 自动生成的方法存根
        ServerSocket ss = new ServerSocket(8080);
        Socket s = ss.accept();
        InputStream is = s.getInputStream();
        OutputStream os = s.getOutputStream();
    
        byte[] bytes = new byte[1024];
        int len = is.read(bytes);
        System.out.println(new String(bytes,0,len));
        os.write("收到谢谢".getBytes());
        os.flush();
    
        s.close();
        ss.close();
    }
    ```

- 练习：服务端和客户端上传和下载文件

  - ```java
    //客户端
    public static void main(String[] args) throws IOException {
        Socket socket = new Socket("127.0.0.1",8080);
        BufferedOutputStream bos1 = new BufferedOutputStream(socket.getOutputStream());
        BufferedInputStream bis = new BufferedInputStream(new FileInputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\4%PVOD}KWN]6F(SW0167WP3.png"));
        byte[] bytes = new byte[1024*1024];
        int len = bis.read(bytes);
        bos1.write(bytes, 0, len);
    
        System.out.println("图片上传完毕");
        bos1.close();
        socket.close();
    }
    ```

  - ```java
    //服务端
    public static void main(String[] args) throws IOException {
        ServerSocket server = new ServerSocket(8080);
        Socket socket = server.accept();
        BufferedInputStream bis = new BufferedInputStream(socket.getInputStream());
        BufferedOutputStream bos = new BufferedOutputStream(new FileOutputStream("C:\\Users\\xpty\\Desktop\\Javabasic\\img.png"));
    
        byte[] bytes = new byte[1024*1024];
        int len = bis.read(bytes);
        bos.write(bytes,0,len);
        System.out.println("图片下载成功");
        bos.close();
    
        socket.close();
        server.close();
    }
    ```

  - 优化：服务端用线程来完成
  
  - ```java
    //客户端
    public static void main(String[] args) throws UnknownHostException, IOException {
    		// TODO 自动生成的方法存根
    		Socket socket = new Socket("127.0.0.1",8080);
    		BufferedOutputStream bos1 = new BufferedOutputStream(socket.getOutputStream());
    		File file = new File("C:\\Users\\xpty\\Desktop\\Javabasic\\4%PVOD}KWN]6F(SW0167WP3.png");	
    		BufferedInputStream bis = new BufferedInputStream(new FileInputStream(file));
    		
    		byte[] bytes = new byte[1024*1024];
    		int len = bis.read(bytes);
    		bos1.write(bytes, 0, len);
    		
    		System.out.println("图片上传完毕");
    		bos1.close();
    		socket.close();
    	}
    ```
  
  - ```java
    public static void main(String[] args) throws Exception{
    		// TODO 自动生成的方法存根
    		ServerSocket server = new ServerSocket(8080);
    		Socket socket = server.accept();
        
    		new Thread(new Runnable() {
    			public void run() {	
    				BufferedInputStream bis;
    				BufferedOutputStream bos;
    				try {
    					bis = new BufferedInputStream(socket.getInputStream());
    					File file = new File("C:\\Users\\xpty\\Desktop\\Javabasic\\upload");
    					if(!file.exists())
    						file.mkdir();
    					String name =  "img" + System.currentTimeMillis()+new Random().nextInt(99999);
    					bos = new BufferedOutputStream(new FileOutputStream(file+"\\"+name+".png"));
    					
    					byte[] bytes = new byte[1024*1024];
    					int len = bis.read(bytes);
    					bos.write(bytes,0,len);
    					System.out.println("图片下载成功");
    					
    				} catch (IOException e) {
    					// TODO 自动生成的 catch 块
    					e.printStackTrace();
    				}finally {		
    					try {
    						socket.close();
    						server.close();
    					} catch (IOException e) {
    						// TODO 自动生成的 catch 块
    						e.printStackTrace();
    					}				
    				}
    			}
    		}).start();
    	}
    ```
  

#### Stream流

- 概述

  - 将集合和数组转换成流的形式，用来解决集合类库的弊端
  - 例如，想要筛选出一个集合中的某些元素，那么我们需要分几次遍历这些元素，需要我们自己编写遍历的代码，这样写是面向过程的写法，因为需要我们来指定实现过程。但我们只是想要结果，**遵循面向对象**的写法，应该只需要指定筛选条件即可。
  - 得到某些元素是我们想要的结果，遍历只是一种实现方式，流的操作为我们提供了另一种**实现方式**而已
  - 流操作不改变原有集合或数组，且**一个流只能调用一次方法**，不能多次调用不同方法
  - 大概步骤： 获取数据源-->过滤-->执行操作获取想要的结果

- 演示

  - ```java
    public static void main(String[] args) {
        List<String> arrays = new ArrayList<>();
        Collections.addAll(arrays,"张无忌","周芷若","张强","张三丰","张牙舞爪");
        arrays.stream()		//将集合转换成流的形式
            .filter(name->name.length()==3)		//该filter的参数是Predicate接口
            .filter(name->name.startsWith("张"))	//同上
            .forEach(name->System.out.println(name));	//张无忌，张三丰
        System.out.println(arrays);	//张无忌，周....，张牙舞爪
    }
    ```

- 使用步骤

  - 获取数据源

    - 对于**Collection 集合**调用**stream()**方法即可获取流对象

    - **Map集合**需要调用方法转换

    - **数组**调用Stream 接口的静态方法**of()**获取流对象

    - ```java
      public static void main(String[] args) {
          //Collection集合中的List和Set集合直接调用stream方法
          List<String> list = new ArrayList<>();
          Stream<String> stream1 = list.stream();
          Set<String> set = new HashSet<>();
          Stream<String> stream2 = set.stream();
          
          //Map集合需要将键和值分开获取流对象
          Map<String,String> map1 = new HashMap<>();
          Set<String> keysets = map1.keySet();
          Collection<String> valuesets = map1.values();
          Set<Entry<String, String>> entrysets = map1.entrySet();
          Stream<String> keysetStream = keysets.stream();
          Stream<String> valuesetStream = valuesets.stream();
          Stream<Entry<String, String>> entrysetStream = entrysets.stream();
          
          //数组调用of方法
          Integer[] integers = {1,2,3,4};
          Stream<Integer> stream3 = Stream.of(integers);
          Long count = stream3.count();	//4
      
      }
      ```

  - 常用方法

    - 终结方法：终结方法不返回流对象，调用后会终止这次流操作
      - void **forEach**(**Consumer**<? super T> con)：用来遍历流对象
      - **Long count**()：返回流中元素个数
    - 延迟方法：延迟方法返回新的Stream流对象，可调用流方法
      - Stream<T> **filter**(**Predicate**<? super T> pre)：用来**过滤**流中元素
      - <R> Stream<R> **map**(**Function**<? super T, ? extends R> mapper )：将一种数据**映射**成另一种数据，跟Function接口目的一致
      - Stream<T> **limit(long maxSize)**：**截取**流中前几位元素
      - Stream<T> skip(long n)：**跳过**流中前几位元素，若n大于流中元素个数则返回0
      - static <T> Stream<T> **concat**(Stream<? extends T> a,  Stream<? extends T> b)：**连接**两个流对象

  - 练习

    - 名字为2个字，前两个为一个流，后两个为一个流，组合这两个流，不是以罗结尾的不要，并用这些流对象创建Person对象并输出

    - ```java
      public static void main(String[] args) {
          List<String> arrays = new ArrayList<>();
          Collections.addAll(arrays, "梅西","c罗","大罗","莫德里奇","克罗斯","穆里尼奥","齐达内","内马尔");
          Stream<String> stream1 = arrays.stream()
              .filter(name->name.length()==2)
              .limit(2);
          Stream<String> stream2 = arrays.stream()
              .filter(name->name.length()==2)
              .skip(2);
          Stream.concat(stream1, stream2)
              .filter(name->name.endsWith("罗"))
              .map(name->new Person(name))
              .forEach(person->System.out.println(person));	//c罗，大罗
      }
      
      public class Person{
          private String name;
          public Person(String name)
          getter and setter
          toString
      }
      ```

#### 方法和构造器引用

- 方法引用概述

  - 在使用lambda表达式传递一块代码时其实是说明了一种解决方案，当这种方案有现成的，那么只需要引用即可
  - 使用步骤和使用lambda表达式一致，只是将lambda表达式换成方法调用

- 简单样例

  - ```java
    //输出字符串
    public static void main(String[] args) {
        method((t)->{
    			System.out.println(t);	//你好，参数经lambda之手后，调用System.out的方法输出
    		});
        method(System.out::println);	//你好，直接调用System.out的方法输出
    }
    
    public static void method(printable pr) {
        pr.printTest("你好");
    }
    
    @FunctionalInterface
    public interface printable {
    	public abstract void printTest(String string);
    }
    ```

- 使用方法

  - **调用某个类的成员方法**

    - 前提是对象存在，成员方法也存在

  - ```java
    //调用方法将字符串变大写再输出
    public static void main(String[] args) {
        method((string)->{
    			printUpper pu = new printUpper();
    			pu.printUpperCase(string);
    		});
    
        printUpper pu = new printUpper();
        method(pu::printUpperCase);	//调用
    }
    
    public static void method(printable pr) {
        pr.printTest("hello");
    }
    
    public class printUpper {
    	public void printUpperCase(String string) {
    		System.out.println(string.toUpperCase());
    	}
    }
    ```

  - **调用某个类的静态方法**

  - ```java
    public static void main(String[] args) {
        int number = method(-10, (num)->{
            return Math.abs(num);
        });
        System.out.println(number);
    
        int number1 = method(-10,Math::abs);	//调用Math类的abs静态方法
        System.out.println(number1);	//-10
    }
    
    public static int method(int i, Calcable cal) {
        return cal.calc(i);
    }
    
    public interface Calcable {
    	public int calc(int number);
    }
    ```

  - **通过super和this调用成员方法**

  - ```java
    public static void main(String[] args) {
        new Man().show();		//因为静态方法内不能使用this和super
    }
    
    public void show() {
        method(()->{
            Human human = new Human();
            human.sayHello();	//使用lambda方法调用父类对象的成员方法
        });
        method(super::sayHello);	//简化lamba的使用
        method(this::sayHello);		//调用子类对象的成员方法
    }
    
    public static void method(sayHello sh) {
        sh.sayhello();
    }
    
    public  void sayHello() {	//子类的sayHello方法
        System.out.println("hello,i am man");
    }
    
    public class Human {		//父类
    	public void sayHello() {	//父类的sayHello方法
    		System.out.println("hello,i am human");
    	}
    }
    
    public interface sayHello {		//接口，接口和方法重名了
    	public abstract void sayhello();
    }
    ```

- 构造器引用

  - 概述

    - 因为构造器的名称和类名一致，并不固定，所以格式为 **类名称::new** 的格式

  - 简单例子

    - ```java
      public static void main(String[] args) {
          Human h1 = method("武磊",(name)->{
              Human human = new Human(name);
              return human;
          });
          System.out.println(h1.getName());
      
          Human h2 = method("c罗",Human::new);
          System.out.println(h2.getName());
      }
      
      public static Human method(String name, newInterface ni) {
          return ni.newObject(name);
      }
      
      ===============
      public interface newInterface {
      	public abstract Human newObject(String name);
      }
      ===============
      public class Human{
          private String name;
          public Human(String name){
              ..
          }
          ...
      }
      ```

- 数据引用

  - 概述

    - 数组的类型和长度已知

    - 格式： **类型[] :: new** ，如： int[] arrays = method(10, int[] :: new);

       



#### 11.final修饰符 √

```java
1.final修饰符的四种用法
   a. 修饰一个类
    	public final class cla{...}  //太监类，不能被继承，所以其方法也不能被重写
   b. 修饰一个方法
        public final void method(){...}  //太监方法，不能被重写，也不能和abstract同时用
   c. 修饰一个局部变量
       基本类型: 不可变指的是变量的数据不可变
          final int num = 10;  //num只能被赋一次值，再赋值会报错
	   引用类型：不可变指的是引用类型的地址不可变
           final Student stu = new Student("张三");
		   stu.setName("李四"); 		 //正确
		   stu = new Student("李四")   //错误   
   d.修饰一个成员变量
        private final int num;
		   由于成员变量具有默认值，所以用了final后必须手动赋值，否则会报错，因为不会再给默认值了
           对于final成员变量，要么直接复制赋值，要么通过构造方法赋值，二选一
           必须保证类当中所有重载的构造方法，都最终会对final的成员变量赋值
2.四种修饰符下的成员变量是否可以调用
     			 public  protected  	无  		private
  同一个类下		  yes		yes			yes			yes
  同一个包下		  yes		yes			yes			no
  不同包的子类	 yes		yes			no			no
  不同包非子类     yes		no  		no			no
               
```

#### 12.内部类 √

```java
一、成员内部类：定义在外部类中
1.格式
    public class Body{ //外用内，需要创建内部类对象
        ...
        public class Heart{ //内用外，随意访问
            ...
        }
        ...
    }
2.成员内部类在本地文件夹下显示为 外部类名称$内部类名称.class;
  外部类显示为 外部类名称.class
3.成员内部类使用方法
      间接使用：在外部类的方法中构建内部类对象，继而使用内部类
      直接使用：外部类.内部类 内部类对象 = new 外部类().new 内部类();
			Body.Heart heart = new Body().new Heart();
			heart.method();
4.变量重名
    在成员内部类的方法中：
    	name 			: 调用内部类中方法内的局部变量
   	 	this.name		: 调用内部类中成员变量
    	Body.this.name  : 调用外部类中的成员变量，此处不用super,是因为内部类和外部类不是继承关系，心脏不是身体。
二、局部内部类 ：定义在外部类的方法中
   1.格式
          public class 外部类{
              ...
              public void method(){  //局部内部类只能作用在外部类的方法中
                  class 局部内部类{   //局部内部类不能用修饰符
                      public void innerMethod(){
                          System.out.println("调用局部内部类方法成功")
                      }
                  }
                  局部内部类 inner = new 局部内部类；
                  inner.innerMethod();
              }
          }
	2.使用 
       	 外部类中在含有局部内部类的方法中构造局部内部类对象，并调用局部内部类的变量或方法
    3.局部内部类的final问题
        局部内部类想要使用所在方法的局部变量时，该局部变量必须是final修饰的（jdk8之后可以不写final），因为，局部变量随方法被压入栈中，而局部内部类被创建在堆当中，局部变量会先消失，所以，只能让局部变量成为不可变的常量，继而直接拷贝不用关心变量变化
        
note：类的修饰符规则
     外部类： public/无
     成员内部类：public/protected/无/private
     局部内部类：什么都不能写
 三、匿名内部类
     1.前提：如果要实现某个接口，但实现该接口的实现类仅创建对象时出现了唯一的一次，那么就可以省掉这个实现类的定义，直接用匿名内部类。若要重复调用对象属性，匿名内部类有重复代码。
     2.格式
       匿名内部类：省略实现类名称
        接口名称 对象名 = new 接口名称() {
        	覆盖重写接口中所有的抽象方法 //将实现类的内容放进大括号内，省略创建实现类过程
   		};  //分号不能少
		对象名.覆盖接口的方法  
            
	   匿名对象： 省略对象名，只能用一次
        new 接口名称(){
            重写的method方法
        }.method();
       
            
```

-  

#### 13.变量类型 

```java
1.类作为成员变量类型
    private Weapon weapon;		//当String类一样使用
2.接口作为成员变量类型
    private Skill skill;	

接口和类做成员变量，都是利用多态的原理实现，要么传实现类或子类，要么直接传匿名内部类或匿名对象。
```

#### 补充 

##### equals 和 ==

- 基本类型
  - '==' : 	比较值
- 对象类型
  - '==' ：比较地址
  - equals
    - 未重写 ： 比较地址
    - 重写 ： 比较属性

##### equals 和 hashcode

- object类中hashcode() 默认由内存地址经哈希算法计算得来，若只重写equals方法而不重写hashcode方法，那么即使两个对象通过equals判断为真，也会因为hashcode值不相等，在hashset中存储两次
- 如果只重写hashcode方法而不重写equals方法，那么两个相等的对象在哈希表因为哈希值相同而进行equals判断时会返回false，他们会在hashset中存储两次



