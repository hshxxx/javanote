# JUC

全称 java.util.concurrent ，此保重增加了在并发编程中常用的工具类。

# volatile

1. 内存模型三大特性：有序性，原子性，可见性。

   **原子性**：操作不会被中断，同一时刻只能有一个线程操作。

   可见性：线程执行的结果在内存中对其他线程的可见性。简单说就是 线程修改了某个变量，但其他线程知不知道变量已经被修改。

   **有序性**：当前线程中所有操作都是有序的，指令重排序不会导致结果发生变化。但在B线程中观察A线程的操作都是无序的，原因是发生了指令重排序。

2. volatile的特点：

   保证有序性，不保证原子性。

   保证内存可见性：被volatile修饰的变量被修改后会强制刷新到主存中，这样其他使用该变量的线程都会知道自己保存的变量副本已经作废，需要从主存中重新获取。

3. **i++ 问题** 来解释为什么volatile不保证原子性

   首先，i++ 操作并不是原子操作，他被分为 **读--改--写** 三步来完成。

   ```java
   读取i；
   temp = i+1;
   i=temp;
   
   ```

   现在有A,B两个线程执行 i++ 操作，假设此时 i= 5，A,B两个线程都读取 i ，temp = 6。A线程先执行了 i = temp(6) 操作，因为 volatile修饰了 变量 i，那么B线程此时的 i 已经作废，所以B 线程中 i = 6。但是因为B线程的 temp 值还为6，所以 B线程执行完 i的值仍旧是 6，而不是7。

4. volatile 与 synchronized 的区别

   - volatile 本质是告诉jvm当前变量在工作内存中是不确定的，需要和主存中的值保持一致。synchronized 本质是 互斥操作，同时只能允许一个线程访问。

   - volatile 只能用在变量上。

     synchronized 可以用在 变量，方法，代码块上。

   - volatile 不会造成线程阻塞，允许同时有多个线程访问变量。

     synchronized 会造成线程阻塞。

# 原子变量与CAS

 java.util.concurrent.atomic 包下的类都满足原子性。他们都使用CAS机制实现。如AtomicBoolean，AtomicInteger，AtomicReference等

1. 什么是CAS

   这个思想中设计3个变量：V 内存中的值， A 内存中的值的拷贝， B 内存中V修改过后的值。只有当 V 和 A 的值相等时才会提交 B。

   举例：现在有线程1和线程2两个线程，它们要将内存中的变量值加1。 内存中存储一个值为 10 的变量V，此时线程1拷贝V的值赋给A，那么线程1中A=10，B=11。此时线程B开始执行并提交，使得V=11。轮到线程1继续执行时，此时 V=11，A=10，线程1发现 A 和 V 的值不相等，那么线程1就放弃修改，从而保证操作满足原子性。

# ConcurrentHashMap

1. HashMap 

   hashmap线程不安全的原因是扩容时会形成死循环。

   扩容方法transfer原码如下：

   ```java
   //table 原来的数组，存储的是Entry对象
   //newTable 扩容后的数组
   void transfer(Entry[] newTable, boolean rehash) {
       int newCapacity = newTable.length;
   	//遍历原来的数组
       for (Entry<K,V> e : table) { 
           while(null != e) {
               //先获取下一个Entry对象
               Entry<K,V> next = e.next;
               //如果需要rehash，那么根据当前Entry的key来hash
               if (rehash) {
                   e.hash = null == e.key ? 0 : hash(e.key);
               }
               //获得当前对象在新的数组中的下标
               int i = indexFor(e.hash, newCapacity);
               //把新的数组中的地址赋给下一个Entry对象
               e.next = newTable[i];
               //将当前Entry对象放到新数组中新的下标下
               newTable[i] = e;
               //e指向下一个Entry对象
               e = next;
           }
       }
   }
   ```

   

2. HashTable 

   因为在操作时采用synchronized锁住了整个数组，很安全但效率非常低。

3. ConcrurrentHashMap

   1.7 版本时采用分段锁

   1.8版本采用 syn+cas 

# CountDownLatch

放在 java.util.concurrent 中。

闭锁：一种同步工具类，可以延迟线程的进度直到其达到终止状态。相当于一扇门，在闭锁达到结束状态之前，这扇门一直关闭，并且没有任何线程通过，当达到结束状态时，这扇门才会打开，并允许所有的线程通过。当闭锁达到结束状态后，将不会再改变状态，因此这扇门会永远保持打开状态。

CountDownLatch是闭锁的实现，它是一个同步辅助类，在完成一组正在其他线程中执行的操作之前，他允许一个或多个线程等待。



实例：

```java
public class CountDownLatchTest {
    public static void main(String[] args) {
        //创建一个计数器值为5的countdownlatch对象
        CountDownLatch countDownLatch = new CountDownLatch(5);
        //需要用同一个countDownLatch
        LatchDemo latchDemo = new LatchDemo(countDownLatch);
        long start = System.currentTimeMillis();
        for (int i=0; i<5;i++){
            new Thread(latchDemo).start();
        }
        try {
            countDownLatch.await();//等countdownlatch计数器的值为0时才执行下面的代码
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        long end = System.currentTimeMillis();
        System.out.println("----------");
        System.out.println(end-start);
    }
}

class LatchDemo implements Runnable{
    private CountDownLatch cdl;

    public LatchDemo(CountDownLatch cdl) {
        this.cdl = cdl;
    }

    @Override
    public void run() {
        try {
            for (int i=0;i<100; i++){
                if(i%2==0){
                    System.out.println(i);
                }
            }
        } finally {
            cdl.countDown(); //每执行一次计数器减1
        }
    }
}
```



# Callable

创建线程的第三种方法。相比较于声明Runnable接口，通过声明Callable接口创建的线程可以**返回对象**，**抛出异常**。

执行Callbale创建的线程需要用FuturnTask实现类来获取返回对象。

通过声明Callable接口创建的线程的执行相当于自己实现了闭锁，只有当线程执行完毕之后才能获取到结果，倘若线程阻塞，那么阻塞结束之前都不能获得到结果。

实例：

```java
public class CallableTest {
    public static void main(String[] args) {
        CallableDemo callableDemo = new CallableDemo();
        FutureTask<Integer> integerFutureTask = new FutureTask<>(callableDemo);
        new Thread(integerFutureTask).start();

        try {
            //只有当线程执行完毕之后才能获取到结果
            Integer sum = integerFutureTask.get();
            System.out.println(sum);
        } catch (Exception e) {
            e.printStackTrace();
        }

    }
}

class CallableDemo implements Callable<Integer> {
    @Override
    public Integer call() throws Exception {
        int sum = 0;
        for (int i=0; i<1000; i++){
            sum +=1;
        }
        return sum;
    }
}
```



# Lock锁

synchronized ：隐式锁，jvm自动加锁和释放锁。

lock：显式锁，需要自己加锁和释放锁。**释放锁的操作必须放在finally中**

实例：

```java
Lock lock = new ReentrantLock();
lock.lock();
try{
	...
}finally {
    lock.unlock();
}
```

## 生产者消费者模型

涉及三个角色：店员，生产者，消费者。

店家需要维护商品数，生产者和消费者需要维护同一个店员。

代码：

```java
public class ProducersAndCustomers {

    public static void main(String[] args) {

        Clerk clerk = new Clerk(0);
        new Thread(new Producer(clerk),"生产者").start();
        new Thread(new Customer(clerk),"消费者").start();

    }
}

class Clerk{
    private int product;

    public Clerk(int product) {
        this.product = product;
    }

    public synchronized void get(){
        if(product>=10){//店员只能有10个库存

            System.out.println("货满了！");
            try {
                this.wait();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }else{
             System.out.println(Thread.currentThread().getName()+"正在进货:"+ ++product);
        this.notifyAll();
        }
       


    }
    public synchronized void sale(){
        if(product <=0){
            System.out.println("没货了！");
            try {
                this.wait();
            } catch (Exception e) {
                e.printStackTrace();
            }
        }else{
             System.out.println(Thread.currentThread().getName()+"正在卖货："+ product--);
        this.notifyAll();
        }
       
    }
}

class Producer implements Runnable{
    private Clerk clerk;

    public Producer(Clerk clerk) {
        this.clerk = clerk;
    }
    
    @Override
    public void run() {
        for (int i=0; i<20; i++){
            clerk.get();
        }
    }
}
class Customer implements Runnable{
    private Clerk clerk;

    public Customer(Clerk clerk) {
        this.clerk = clerk;
    }

    @Override
    public void run() {
        for (int i=0; i<20; i++){
            clerk.sale();
        }
    }
}
```

## 虚假唤醒

虚假唤醒：当一个条件满足时，很多线程都被唤醒了，但是只有其中部分是有用的唤醒，其它的唤醒都是无用功。

在上面的生产者消费者例子中，将生产产品的get方法改为 product>=1。也就是说限制它每次只能生产一件产品。

```java
public synchronized void get(){
    if(product>=1){
        System.out.println("货满了！");
        try {
            this.wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }else{
       System.out.println(Thread.currentThread().getName()+"正在进货:"+ ++product);
        this.notifyAll();
    }
}
public synchronized void sale(){
    if(product <=0){
        System.out.println("没货了！");
        try {
            this.wait(); 
        } catch (Exception e) {
            e.printStackTrace();
        }
    }else{
        System.out.println(Thread.currentThread().getName()+"正在卖货："+ product--);
        this.notifyAll();
    }
```

解释：

假如此时product=0，消费者线程先抢到了锁，消费者线程判断之后发现没有产品可以销售了，所以调用wait()方法，进入阻塞状态并释放了锁。接下来生产者线程抢到了锁，生产者线程发现没有产品了，需要自己生产产品，所以现在product=1，并且调用notify()方法唤醒了消费者线程。此时消费者线程和生产者线程都处于唤醒状态，假如又是消费者线程先抢到了锁，那么消费者线程从刚才阻塞的地方继续执行，这导致了消费者线程并没有买这个产品，product仍旧为1。接下来该生产者线程执行，生产者线程判断之后发现产品数是满的，不需要继续生产，生产者线程便调用wait()方法，进入阻塞状态。此时若消费者线程不再执行，那么生产者线程将一直阻塞，系统停止不下来。

原因：

因为使用if判断语句来实现等待和唤醒机制，所以导致等待和唤醒在线程中一次只能执行其中一个。

解决方法：

**将else括号删除**。

修改后代码：

```java
public synchronized void get(){
    if (product>=1){
        System.out.println("货满了！");
        try {
            this.wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    System.out.println(Thread.currentThread().getName()+"正在进货:"+ ++product);
    this.notifyAll();

}
public synchronized void sale(){
    if (product <=0){
        System.out.println("没货了！");
        try {
            this.wait();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    System.out.println(Thread.currentThread().getName()+"正在卖货："+ product--);
    this.notifyAll();
}
```

**新问题**：如果只有一个消费者和生产者线程那么只删除else语句可以解决问题，但如果有若干生产者和消费者线程时，新的问题又出现了。

假如有消费者线程1和消费者线程2。此时product=0，消费者线程1先争取到了锁，然后消费者线程1判断后发现没货了，所以消费者线程1进入等待状态并释放锁。若消费者线程2又争到了锁，然后消费者线程2判断之后也发现没货了，所以消费者线程2也进入了等待状态并释放了锁。此时生产者线程开始执行并生产了一件产品并唤醒了两个消费者线程。假如消费者线程1争到了锁，使得product=0，然后消费者线程2又争到了锁，使得product= -1。卖出的产品不能是负数的，所以只删除else语句是不能完全解决问题的。

**解决方法： 使用while 替代 if**。当消费者线程2被唤醒时它会先判断while的条件是否满足，发现product=0，那么它会继续等待。

再次修改后的代码：

```java
public synchronized void get(){
    while (product>=1){

        System.out.println("货满了！");
        try {
            this.wait();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }
    System.out.println(Thread.currentThread().getName()+"正在进货:"+ ++product);
    this.notify();
}
public synchronized void sale(){
    while (product <=0){
        System.out.println("没货了！");
        try {
            this.wait();
        } catch (Exception e) {
            e.printStackTrace();
        }
    }
    System.out.println(Thread.currentThread().getName()+"正在卖货："+ product--);
    this.notify();
}
```





## Condition 线程通信

使用synchronized 不需要自己开锁和闭锁。

使用 Lock 需要自己开锁和闭锁，通过condition 对象实现。

Object类中有wait,notify,notifyAll方法，在Condition类中也有**await, signal, signalAll** 方法与之对应。

condition对象实质是绑定到一个锁上。要为特定的 Lock对象获得Condition，需要使用Lock对象的 newCondition()  方法生成。

用Lock实现生产者消费者模型:

```java
public class ConditionTest {
    public static void main(String[] args) {
        Clerk1 clerk1 = new Clerk1(0);
        new Thread(new Producer1(clerk1),"生产者1").start();
        new Thread(new Customer1(clerk1),"消费者1").start();
        new Thread(new Customer1(clerk1),"消费者2").start();
        new Thread(new Producer1(clerk1),"生产者2").start();

    }
}
class Clerk1{
    private int product;
    private Lock lock = new ReentrantLock();
    private Condition condition = lock.newCondition();

    public Clerk1(int product) {
        this.product = product;
    }

    public void get(){
        lock.lock();
        try {
            while (product>=1){
                System.out.println("货满了！");
                try {
                    condition.await();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName()+"正在进货:"+ ++product);
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }
    public synchronized void sale(){
        lock.lock();
        try {
            while (product <=0){
                System.out.println("没货了！");
                try {
                    condition.await();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            System.out.println(Thread.currentThread().getName()+"正在卖货："+ product--);
            condition.signalAll();
        }finally {
            lock.unlock();
        }
    }
}

class Producer1 implements Runnable{
    private Clerk1 clerk1;

    public Producer1(Clerk1 clerk1) {
        this.clerk1 = clerk1;
    }

    @Override
    public void run() {
        for (int i=0; i<20; i++){
            clerk1.get();
        }
    }
}
class Customer1 implements Runnable{
    private Clerk1 clerk1;

    public Customer1(Clerk1 clerk1) {
        this.clerk1 = clerk1;
    }

    @Override
    public void run() {
        for (int i=0; i<20; i++){
            clerk1.sale();
        }
    }
}
```



## 交替打印

问题描述：三个线程分别打印A，B，C。编写程序使得他们交替打印 ABCABCABC... ，一共打印10轮。

代码：比问题更难，交替打印，每次打印5次。

```java
public class AlterPrintTest {
    public static void main(String[] args) {
        int totalLoop = 20;
        AlterDemo alterDemo = new AlterDemo();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i=1; i<=totalLoop;i++){
                     alterDemo.printA(i);
                }
            }
        },"A").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i=1; i<=totalLoop;i++){
                    alterDemo.printB(i);
                }
            }
        },"B").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i=1; i<=totalLoop;i++){
                    alterDemo.printC(i);
                }
            }
        },"C").start();
    }
}

class AlterDemo{
    private int id=1; //用来标识线程
    private Lock lock = new ReentrantLock();
    Condition condition1 = lock.newCondition();
    Condition condition2 = lock.newCondition();
    Condition condition3 = lock.newCondition();

    public void printA(int totalLoop){
        lock.lock();
        try {
            while (id != 1){
                try {
                    condition1.await();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            //2.打印A
            for (int i=0; i<5;i++){
                System.out.println(Thread.currentThread().getName()+"正在打印:"+"\t" +"A"+"\t是第"+totalLoop+"轮");
            }
            //3.唤醒打印B的线程
            id=2;
            condition2.signal();
        }finally {
            lock.unlock();
        }
    }

    public void printB(int totalLoop){
        lock.lock();
        try {
            while (id != 2){
                try {
                    condition2.await();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            //2.打印B
            for (int i=0; i<5;i++){
                System.out.println(Thread.currentThread().getName()+"正在打印:"+"\t" +"B"+"\t是第"+totalLoop+"轮");
            }
            //3.唤醒打印C的线程
            id=3;
            condition3.signal();
        }finally {
            lock.unlock();
        }
    }
    public void printC(int totalLoop){
        lock.lock();
        try {
            if (id != 3){
                try {
                    condition3.await();
                } catch (Exception e) {
                    e.printStackTrace();
                }
            }
            //2.打印C
            for (int i=0; i<5;i++){
                System.out.println(Thread.currentThread().getName()+"正在打印:"+"\t" +"C"+"\t是第"+totalLoop+"轮");
            }
            //3.唤醒打印A的线程
            System.out.println("-------------------");
            id=1;
            condition1.signal();
        }finally {
            lock.unlock();
        }
    }

}

```



# ReadWriteLock 锁

写写 ，读写 操作需要互斥，不能同时写和同时读写。

代码：

```java
public class ReadAndWriteTest {
    public static void main(String[] args) {
        //读写锁的创建
        ReadAndWriteDemo readAndWriteDemo = new ReadAndWriteDemo();
        new Thread(new Runnable() {
            @Override
            public void run() {
                double random = Math.random()*100;
                readAndWriteDemo.write((int)random);
            }
        },"写线程").start();
        new Thread(new Runnable() {
            @Override
            public void run() {
                for (int i=0;i<20;i++){
                    readAndWriteDemo.read();
                }
            }
        },"读线程").start();
    }
}

class ReadAndWriteDemo{
    private ReentrantReadWriteLock lock = new ReentrantReadWriteLock();
    private int i;

    public void write(int number){
        lock.writeLock().lock();
        try {
            this.i = number;
            System.out.println(Thread.currentThread().getName()+i);
        }finally {
            lock.writeLock().unlock();
        }
    }
    public void read(){
        lock.readLock().lock();
        try {
            System.out.println(Thread.currentThread().getName()+i);
        }finally {
            lock.readLock().unlock();
        }
    }
}
```

# 线程八锁

1. 非静态方法的锁默认为 this (也就是当前线程实现类的对象)，静态方法的锁对应的是 类.Class  (也就是当前线程实现类本身)。它们没有竞争同一个锁。
2. 某一时刻内，只能有一个线程持有锁，无论有几个方法。
3. 注意多个线程之间是否竞争同一个类的锁还是同一个对象的锁



# 线程池

线程池提供了一个线程队列，队列中保存着所有等待状态的线程。避免了频繁创建和销毁线程的开销，提高响应速度。

**线程池体系结构**：

java.util.concurrent.Executor ：负责线程的使用与调度的根接口

​	ExecutorService ：子接口，线程池的主要接口

​		ThreadPoolExecutor：线程池的实现类

​		ScheduledExecutorService：子接口，负责线程的调度

​				ScheduledThreadPoolExecutor：继承ThreadPoolExecutor，实现ScheduledExecutorService

java.util.concurrent.Executors：线程池的工具类

**Executors 工具类中的方法**：

1.  **newFixedThreadPool(int nThreads)**
   创建一个固定线程数量的线程池，每当提交一个任务就创建一个线程，直到达到线程池的最大数量，这时线程规模将不再变化，新的任务会暂存在任务队列中，待有线程空闲时便处理任务。
2. **newCachedThreadPool()**
   创建一个可缓存的线程池，如果线程池的规模超过了处理需求，将自动回收空闲线程，而当需求增加时，则可以自动添加新线程，线程池的规模不存在任何限制。
3.  **newSingleThreadExecutor()**
   这是一个单线程的Executor，它的特点是能确保依照任务在队列中的顺序来串行执行，适用于保证异步执行顺序的场景。
4. **newScheduledThreadPool(int corePoolSize)（推荐）**
   创建了一个固定长度的线程池，以定时的方式来执行任务，适用于定期执行任务的场景。
5. **newWorkStealingPool** 
   使用ForkJoinPool ,多任务队列的固定并行度，适合任务执行时长不均匀的场景



线程池的使用：

1. 固定大小的线程池的使用

```java
public class ThreadPoolTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {

        ExecutorService executorService = Executors.newFixedThreadPool(6);
        ThreadDemo threadDemo = new ThreadDemo();
        executorService.submit(new Runnable() {
            @Override
            public void run() {
                for (int i=0 ;i<1; i++){
                    threadDemo.add();
                }
                System.out.println("-------------");
            }
        });
        Future<Integer> result = executorService.submit(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                int sum = 0;
                for (int i = 0; i < 100; i++) {
                    sum += i;
                }
                return sum;
            }
        });
        System.out.println(result.get());
        System.out.println("========");
        executorService.shutdown();//使用完线程池要关闭
    }

}

class ThreadDemo{
    public void add(){
        for(int i=0;i<100;i++){
            System.out.println(i);
        }
    }
}

```

2. 固定大小的定时的线程池的使用

```java
public class ThreadPoolTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ScheduledExecutorService scheduledExecutorService = Executors.newScheduledThreadPool(5);
        ScheduledFuture<Integer> schedule = scheduledExecutorService.schedule(new Callable<Integer>() {
            @Override
            public Integer call() throws Exception {
                int sum = 0;
                for (int i = 0; i < 100; i++) {
                    sum += i;
                }
                return sum;
            }
        }, 3, TimeUnit.SECONDS); //延迟3秒执行
        System.out.println(schedule.get());
        scheduledExecutorService.shutdown();
    }
}

```



# ForkJoinPool 分支合并框架













































