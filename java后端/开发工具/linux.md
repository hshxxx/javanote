# linux学习路线

![image-20201119120354372](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201119120354372.png)

# linux 知识全概

![image-20201119171112930](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201119171112930.png)



# 虚拟机

## vmware

## centos

账号：root 密码：123456

## vmtools

实现本机系统和虚拟机中centos系统共享数据。包括centos和windows之间复制粘贴和共享文件夹。

1. vmtools 的安装

   在vmware 中 ：虚拟机--> 安装 vmware tool。

   将生成的 vmwaretools.... tar.gz 结尾的文件复制到 文件系统的 opt 目录下，然后安装（具体步骤略过）。重启centos即可实现跨系统复制粘贴。

   ----------

   在vmware 中 ：虚拟机--> 设置--->选项--->设置共享文件夹--->选定一个共享文件夹。

   打开centos的 文件系统 ---> mnt文件夹---> hgfs文件夹--->共享文件夹

   共享文件夹设置完毕。

   

   

# linux 目录结构

linux只有一个根目录 （“ / ”），在此目录下创建其他目录。在linux中一切皆文件。

linux目录结构图：每一个文件夹有规矩的。

![image-20201120111940710](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201120111940710.png)

| 目录                                      | 介绍                                                         |
| ----------------------------------------- | ------------------------------------------------------------ |
| /bin  (重要)(/user/bin,  /user/local/bin) | binary的缩写，这个目录存放着最常使用的命令                   |
| /sbin                                     | super user bin，存放系统管理员使用的系统管理程序             |
| /home (重要)                              | 存放普通用户的主目录，每一个用户都有一个自己的目录，目录名就是账号名 |
| /root (重要)                              | 系统管理员的目录                                             |
| /lib                                      | 系统开机所需要最基本的动态连接共享库                         |
| /etc (重要)                               | 所有的系统管理所需要的配置文件和子目录                       |
| /user(重要)                               | 用户的很多应用程序和文件都放在这个目录下，类似于windows的program files目录 |
| /boot (重要)                              | 启动linux时需要的核心文件                                    |
| /proc                                     | 虚拟目录，它是系统内存的映射，可以获取系统信息               |
| /srv                                      | service所系，存放服务启动之后需要提取的数据                  |
| /sys                                      | 安装了linux2.6内核中新出现的文件系统 sysfs                   |
| /temp                                     | 存放临时文件                                                 |
| /dev                                      | 所有硬件用文件的形式存储，类似windows的文件管理器            |
| /media (重要)                             | linux系统会自动识别设备，如u盘等，linux会将识别到的设备挂载到这个目录下 |
| /mnt (重要)                               | 该目录让用户临时挂载别的文件系统，用户可以将外部的存储挂载到/mnt/下，然后就可以查看外部存储的内容 |
| /opt                                      | **放置主机额外的安装软件的目录**，比如MySQL就可以放置到该目录下。 |
| /user/local (重要)                        | 另一个给主机安装软件的**安装目录**，一般通过编译源码方式安装的程序。MySQL被安装到这个目录。 |
| /var (重要)                               | 存放着不断扩充着的东西，习惯将经常被修改的目录放在该目录下，如日志文件。 |
| /selinux                                  | SELinux 是一种安全子系统，控制程序只能访问特定文件。         |

总结：

1. linux中一切皆文件，任何对象都是以文件的形式存在着。
2. linux中目录的设定是有规矩的，不能任意存放和创建。
3. linux目录树只有一个根目录，不像widows中有c,d盘之分。



# xshell和xftp

xshell：远程登陆linux

xftp：远程上传和下载文件

建立xshell和xftp连接之前需要查看linux系统的sshd服务是否打开。

xshell 和 xftp图解：

![image-20201120122130384](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201120122130384.png)

**xshell：**

1.配置xshell连接

在虚拟机的命令行中输入 ifconfi 命令，查看虚拟机为linux分配的ip地址。在xshell中新建一个session，会话的名称和主机都填 linux的IP地址即可。

2.使用 xshell 即可操纵远程的 linux 系统

**xftp**:

1.配置xftp连接：和xshell配置连接类似，需要linux系统的IP地址。

# vi和vim

所有的linux系统都会内置 vi文本编辑器，vim是vi的增强版。

1. vim 的三种模式
   - 正常模式：用 **vim 命令**打开一个目录默认就是正常模式，可以查看，删除内容，但不能编辑。
   - 编辑模式：按下 **i** 进入编辑模式。
   - 命令行模式：输入**：**进入命令行模式，然后就可以完成读取，存盘，替换，离开vim的操作等。wq表示保存并退出，q表示退出，q! 表示强制退出。

2. 使用esc 按键切换模式。

## 快捷键



1. 拷贝当前行：在正常模式下  yy；拷贝当前行向下5行： 5yy；粘贴： p
2. 删除当前行：在正常模式下 dd；删除当前行向下5行：5dd
3. 光标移动到首行：在正常模式下 gg； 移动到尾行 G
4. 查找某个单词： 编辑模式下 /单词 + 回车，按 n 是查找下一个
5. 设置行号：命令行模式下 set nu ;  关闭行号： set nonu
6. 撤销刚才的编辑： 命令行模式下 n + 回车



# linux 用户管理

1. 开关机命令

   - shutdown -h now ： 立刻关机
   - shutdown -h 1  ：一分钟后关机
   - shutdown -r now   ：现在重启计算机
   - halt  ：关机
   - reboot ： 现在重启计算机
   - **sync：把内存中数据同步到磁盘，关机和重起前务必执行此命令**

2. 用户的登陆和注销

   - 切换到管理员账号： su -用户名
   - 注销账户：logout 

3. 用户的创建

   ![image-20201121092840228](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201121092840228.png)

   1. 用户组：每一个linux用户都属于一个用户组，linux可以对一个用户组中的所有用户集中管理。
   2. 用户家目录：每一个用户登陆后都有一个自己的家目录，如 /home/xm，对于管理员来说就是 /root
   3. 用户的创建： 
      - **useradd  用户名**：创建一个用户和与用户名同名的用户组。
      - **useradd -d  目录名 用户名** ：将用户创建到指定目录

4. 设置和修改用户密码： **passwd  用户名** 

5. 删除用户：

   - **userdel  用户名** ：删除用户
   - **userdel -r 用户名** ：删除用户和它的家目录

6. 查看用户信息： **id 用户名**

7. 切换用户：**su 用户名**；  切换回用户：**exit**

8. 创建用户组：**groupadd  组名**

   删除用户组：**groupdel 组名**

   增加用户时直接指定用户组：**useradd -g 用户组 用户名**

   修改用户的用户组：**groupmod -g 用户组 用户名**

9. 用户和组的相关文件

   - /etc/password ：存放用户信息
   - /etc/shadow：存放加密之后的用户密码
   - /etc/group：存放用户组的信息



# linux指令

## 运行级别

某个程序在一个运行级别里面运行在另一个级别就不启动。

![image-20201121103414099](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201121103414099.png)

切换运行级别： **init [0123456]**

面试题：如何找回root用户密码。

​			进入单用户运行级别，不需要输入root密码即可登陆。

​			启动虚拟机 ---> 点enter 进入一个引导界面 --> 按 e 进入新界面设置运行级别为1，然后退出 --> 		按b 开始启动系统。

## 帮助指令：

- man 指令/配置文件：显示指令或配置文件的介绍。
- help 指令 ：获取指令的功能描述（只能查看shell内置的指令）
- info 指令 ：比man命令显示的更详细。

## 文件目录类

1. 显示当前绝对路径：pwd
2. 显示当前路径下文件：
   - ls ：只显示目录名
   - ll ：每个目录占一行，显示目录的详细信息
   - ll/ls -a ：显示隐藏文件
   - ls -al ：显示隐藏文件且每个文件独占一行
3. cd 指令
   - cd ~ ：回到自己的家目录
   - cd .. ：回到上一级目录
4. mkdir与 rmdir 指令
   - mkdir 目录名：创建目录
   - mkdir -p 多级目录名：创建多级目录
   - rmdir 目录名 ：删除目录
   - rm -rf ：删除包含文件的目录
5. touch 和 cp 指令
   - touch hello.txt：创建一个空文件
   - cp 源文件 指定目录 ：将源文件拷贝到指定目录（拷贝单个文件）
   - cp -r 源目录  指定目录：将源目录下的内容都拷贝到指定目录（拷贝整个目录）
6. rm 和 mv 指令
   - rm 文件名 ：删除文件并提示
     - rm -r 目录名 ： 递归删除整个文件夹。会有提示，输入y 或 n 
     - rm -f 文件名：直接删除文件不提示
     - rm -rf 目录名/文件名 ：直接删除
   - mv 旧名字文件  新名字文件 ：修改文件的名字
     - mv 旧目录  新目录 ：将文件移动到新目录下

7. cat 和 more 和 less指令
   - cat 文件名 ：查看文件内容
     - cat -n 文件名 ： 显示行号查看
     - cat 文件名 | more ：利用管道可以分页查看文件，按空格看下一页
   - more 文件名： 查看文件内容，有很多快捷键方便查看文件内容
   - less 文件名：查看文件内容，相比more指令，less没有一次将整个文件加载之后才显示，对于显示大型文件有更高效率
8.  ‘>' 和 >> 指令
   - ls -l > 文件名 ：将 ls -l 遍历的内容覆盖到文件中
   - ls -l >>文件名 ：将ls-l 遍历的内容追加到文件内容之后
   - cat 文件1 > 文件2：同理
9. echo ，head ,tail 指令
   - echo "hello"  ：将hello输出到控制台
   - head 文件：显示文件前10行
     - head -n 5 文件名：显示文件前5行内容
   - tail 文件：查看文件后10行
     - tail -n 5 文件：查看文件后5行
     - tail -f 文件：追踪该文档的所有更新，会实时打印该文件的变化
10. ln 指令
    - ln -s 源文件或目录 软链接名：生成一个访问特定文件或目录的地址。 rm -rf 删除
11. history 指令
    - histor ：查看已经执行过的历史命令
    - history 10 ：查看10个刚执行过的命令
    - ! 行号：执行行号对应的命令

## 时间日期类

1. date 指令
   - date ：显示当前时间
   - date +%Y：显示当前年份，m,d显示月份，哪一天
   - date -s 字符串时间：设置当前时间为字符串时间
2. cal 指令
   - cal ：显示本月日历
   - cal 2020：显示2020年所有月份

## 搜索查找类

1. find 指令
   - find  /home -name hello.txt：查找 /home 下 叫hello.txt 文件
   - find /home -user zhangsan：查找 /home 下 叫zhangsan的用户
   - find /  -size +20M：查找整个linux系统中尺寸大于20M的文件
     - 20M ：等于20M
     - -20M：小于20M
2. locate 指令
   - locate hello.txt：打印hello.txt的路径
   - 使用locate指令前，需要使用updatedb指令创建locate数据库
3. grep 指令 和 管道符号 |
   - grep -n "hello.word" hello.txt：返回hello.txt 文件中包含“hello.world"的行和行号
   - grep -i "hello.word" hello.txt：忽略大小写，返回hello.txt 文件中包含“hello.world"的行
   - 管道符号 | ：表示将前一个命令的处理结果输出传递给后面的命令处理。
   - cat hello.txt | grep -i hello：忽略大小写，返回hello.txt 文件中包含“hello"的行

## 压缩和解压类

1. gzip 和 gunzip 指令
   - gzip 文件名：将文件压缩成 .gz 格式，不会保留原来的文件。
   - gunzip 文件.gz：将文件解压
2. zip 和 unzip 指令
   - zip -r dir1.zip /home/dir/：将/home/dir/下所有文件压缩打包成dir1.zip文件，-r 表示递归压缩
   - unzip -d /home/dirunzip/ dir1.zip：将dir1.zip文件解压到 /home/dirunzip/ 下
3. tar 指令
   - tar -zcvf  a.tar.gz a1.txt a2.txt ：将a1.txt 和a2.txt文件打包压缩成 a.tat.gz 文件
     - -z：打包同时压缩
     - -c：产生.tar 打包文件
     - -v：显示详细信息
     - -f：指定压缩后的文件名
     - -x：解压 .tar 文件
   - tar -zcvf  myhome.tar.gz /home/：将/home/ 下所有文件打包压缩成myhome.tar.gz 文件
   - tar -zxvf myhome.tar.gz ：将myhome.tar.gz解压到当前目录
   - tar -zxvf myhome.tar.gz **-C**  /home/dir1 ：将myhome.tar.gz解压到/home/dir1目录

# 组管理

一个文件或目录属于三个对象，分别是 所有者，所在组，其他组。

所有者一般就是创建文件的那个用户。

1. 查看文件或目录的所有者：ls -ahl
2. 修改文件的所有者：chown 用户名 文件名

某个用户创建了一个文件后，默认这个文件的所在组就是用户所在组。

1. 查看文件或目录的所在组：ls -ahl
2. 修改文件所在组： chgrp 组名 文件名

除文件的所有者和所在组的用户外，系统的其他用户都是文件的其他组。

1. 改变用户所在组： 
   - usermod -g 组名 用户名
   - usermod -d 目录名 用户名 ：改变用户登陆的初始目录

# 文件权限

d rwx --- ---：

- 第一位用来表示该对象的类型
  - d ：代表这是目录
  - -代表普通文件
  - c：代表字符设备
  - b：代表块文件，硬盘
- 后面9位每3位为一组，按顺序分别代表 所有者，所在组，其他组的权限。
  - r 代表 读权限，值为4
  - w 代表 写权限，值为2。拥有写权限才能修改，删除文件或目录
  - x 代表 执行权限，值为1

1. 修改权限
   - chmod u=rwx,g=rx,o=x 文件或目录名：修改所有者，所在组，其他组权限
   - chmod o+w 文件目录名：+ 表示添加
   - chmod a-x 文件目录名：a 表示所有人，- 表示取消
   - chmod 751 文件或目录名：等同于第一个例子
2. 修改文件所有者
   - chown newowner file ：改变文件的所有者
   - chown newowner:newgroup file：改变文件的所有者和所在组
   - chown -R newowner:newgroup file：若File是目录，那么递归改变目录下所有文件
3. 修改文件的所在组
   - chgrp newgrouo file：改变文件的所有者

# 任务调度

 任务调度指在某个时间执行的特定的命令和程序。任务调度分为系统工作（定时执行病毒查杀）和个别用户工作（定时对数据库备份）。

![image-20201122103633181](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122103633181.png)



1. crontab 指令

   - 每分钟查看一次/etc/下所有文件，并将其追加到 /tmp/to.txt中

     1. crontab -e ，在crontab中输入 */1 * * * * ls -l /etc/ /tmp/to.txt ，五个星号分别表示 一个小时的第几分钟，一天的第几个小时，一个月的第几天，一年的第几个月，一周的第几天，此处的命令意思为：每个小时的每一分钟执行后面的命令。
     2. 每一分钟to.txt都会更新

   - 特殊字符

     ![image-20201122110248538](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122110248538.png)

   - 案例

     ![image-20201122114640278](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122114640278.png)



# 磁盘分区

![image-20201122115148578](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122115148578.png)

gtp 分区比mbr分区有更多的主分区，且容量更大。

![image-20201122115239831](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122115239831.png)



## linux 分区

1. 原理：

![image-20201122115524912](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122115524912.png)

![image-20201122115616632](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122115616632.png)

2. linux磁盘分类

![image-20201122120907337](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122120907337.png)

3. 查看磁盘分区情况

   指令： lsblk -f

4. 给虚拟机挂载一块新硬盘

   ![image-20201122121529444](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122121529444.png)

5. 查看磁盘使用情况：df -h

6. 查看指定目录的磁盘占用情况

   - du -h /目录 ：带计量单位
   - du -s /目录：指定目录占用大小汇总
   - du -a /目录：含文件
   - du -max-depth=1 /目录：子目录深度
   - du -c /目录：列出明细的同时，增加汇总值



# 网络配置

1. linux网络配置原理图

![image-20201122124520301](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122124520301.png)

2. 查看网络IP和网关：在虚拟机的虚拟网络编辑器中查看

3. linux网络环境配置

   - 自动获取：在centos中设置

   - 指定固定IP：使用指令  vi /etc/sysconfig/network-scripts/ifcfg-etho 来修改IP地址，eth0表示网卡

     ![image-20201122125544604](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122125544604.png)

# 进程管理

1. 基本介绍

   ![image-20201122125925224](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122125925224.png)

2. 显示系统执行的进程

   - ps -a：显示当前终端的所有进程信息
   - ps -u：以用户的格式显示进程信息
   - ps -x：显示后台进程运行的参数 

   ![image-20201122133447797](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122133447797.png)

    - ps -ef：以全格式显示当前所有的进程

      ![image-20201122133847020](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122133847020.png)

3. 终止进程

   - kill 进程号：通过进程号杀死进程
   - kill -9 进程号：强迫进程立即停止

## 服务管理

1. 服务的介绍：

![image-20201122134503300](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122134503300.png)

2. service 管理指令：service 服务名 start | stop | restart | reload | status

3. 服务的运行级别

   ![image-20201122135321162](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122135321162.png)

4. chkconfig 指令 ：给各个运行级别设置自启动或自关闭，重启后才能生效。

   - chkconfig --list：查看系统中运行的各个服务

   - chkconfig --list | grep 服务名：只查看特定的服务
   - chkconfig 服务名 --list：同上
   - chkconfig --level 5 服务名 on/off：将服务在运行级别为5的情况下，自启动或自关闭

## 动态监控

1. 动态监控进程：top

   top 与ps指令相似，但top在执行一段时间可以更新正在运行的进程。

   - top ：显示正在执行的进程。
     - 按u筛选用户
     - 按k输人进程号来杀死进程

   - top -d 秒数：指定top指令每个几秒更新，默认三秒
   - top -i ：使top不显示任何闲置或者僵尸进程
   - top -p：通过指定进程ID来只监控某个进程的状态

2. 监控网络状态

   - netstat -an：按一定顺序排列输出
   - netstat -p：显示哪个进程在调用

# RPM和YUM

1. RPM

   一种用于互联网下载包的打包及安装工具。

   - rpm -qa | grep firefox：查询已安装的firefox

   ![image-20201122154505256](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201122154505256.png)

   - 卸载rpm包
     - rpm -e  rpm包名
   - 安装rpm包
     - 先找到firefox的安装rpm包，然后给虚拟机挂载上centos镜像文件，然后到/media/下找相应的rpm包，找到后将rpm包复制到 /opt/ 目录下
     - rpm -ivh rpm包 ：安装相应的rpm包

2. yum

   一个shell前端软件包管理器，基于rpm包管理，能够从指定的服务器自动下载rpm包并且安装，可以自动处理依赖性关系，并且一次性安装所有依赖的软件包。

   - yum list | grep 软件名：查询yum服务器是否有需要安装的软件
   
   - yum install 软件名：安装指定的yum包
   
     

