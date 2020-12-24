# Git相关介绍

## 版本控制工具

集中式：SVN，数据保存在一个服务器上。

分布式：Git，数据保存在每一台电脑上。避免单点故障。

## 版本控制工具的功能

1. 协同修改：多人同时修改同一个文件。
2. 数据备份：不仅保存目录和文件的当前状态，还能保存每一个提交过的历史状态。
3. 版本管理：保存每个版本时做到不保存重复数据。
4. 权限控制：团队中的开发人员进行权限控制，审核团队外的开发者的代码。
5. 历史记录：查看修改人，时间，内容。将本地文件恢复到某一个历史状态。
6. 分支管理：开发时多条生产线同时推进。

# Git结构

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101110952853.png" alt="image-20201101110952853" style="zoom:50%;" />

​	index ：暂存区

​	working tree：工作区

## Git和代码托管中心

代码托管中心的任务就是维护远程库。

1. 局域网下：GitLab
2. 互联网下：github，码云

## 本地库与远程库

1. 团队内部协作

   <img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101111536196.png" alt="image-20201101111536196" style="zoom:80%;" />

   

2. 团队内部与外部协作

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101112608656.png" alt="image-20201101112608656" style="zoom: 67%;" />

# Git 命令行操作

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101113757290.png" alt="image-20201101113757290" style="zoom:80%;" />

## 本地库操作

### 本地库初始化

就是创建一个本地库。

命令：

1. git init ：初始化**一个 .git 隐藏文件**，这个.git 文件存储本地库相关的子目录和文件，不要修改它。
2. ll：显示当前目录的文件。
3. ll .git：显示 .git 文件。
4. ls -la ：显示当前目录的隐藏文件。

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101121240642.png" alt="image-20201101121240642" style="zoom:80%;" />

#### 设置签名

区分不同开发人员的身份，这里的签名和登陆代码管理中心的账号是没有任何关系的。

签名分为两类：优先使用项目级别。

- 项目（仓库）级别：仅在当前本地库范围内有效。
- 系统用户级别：登陆当前操作系统的用户范围内有效。一般使用这个。

1.**项目级别**

​	命令：git config user.name  NAME,   git config user.email  EMAIL;

​	![image-20201101162420034](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101162420034.png)

签名存储的位置：git/config 文件中

![image-20201101162440390](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101162440390.png)

2. **系统用户级别**

   命令：git config --global NAME, git config --global EMAIL;

![image-20201101162941077](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101162941077.png)

签名存储的位置：当前用户所在根目录下的 .gitconfig 中

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101163009055.png" alt="image-20201101163009055"  />

### 基本操作

#### 创建，修改，添加，提交

命令：

1. **查看状态： git status**

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101164806858.png" alt="image-20201101164806858"  />on branch master ：当前在 master这个分支上

no commits yet：还没有提交过数据，本地库是空的。

nothing to commit (create/copy files and use "git add" to track)：没什么可提交的，说的是暂存区是空的。



2. **新建文件：vim good.txt** ； 使用的是linux中的vim指令。

如果good.txt 文件不存在，那么会新建这个文件。如果存在，那么会修改这个文件。

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101165844404.png" alt="image-20201101165844404" style="zoom:50%;" />

修改之后自然需要保存，先按esc 退出编辑，然后输入 : 说明你要输入命令了。w 代表write，q 代表 quit，然后回车。这样这个good.txt文件就创建好了。 

此时再次输入git status 命令，显示如下。

![image-20201101170139809](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101170139809.png)

untracked files ... ：当前创建了一个good.txt 文件，可以用 git add 命令把这个文件添加到暂存区。



3. **将文件添加到暂存区：git add** 

   ![image-20201101170546231](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101170546231.png)

至此，good.txt 文件已经被添加到暂存区中了，我们再次输入git status。

![image-20201101170656924](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101170656924.png)

还是在master分支，还是没有提交。但暂存区中刚添加的good.txt文件还可以撤回。



4. **将缓存区文件撤回到工作区：git rm --cached**

我们输入git rm --cached good.txt 命令，显示如下。

![image-20201101170941171](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101170941171.png)

good.txt 文件又变红了，说明good.txt文件又处于刚才还未添加到暂存区的状态了。



5. **将缓存区文件提交到本地仓库：git commit**

   上面将good.txt撤回到了工作区，我们先用 git add 命令将它添加到暂存区，然后使用 git commit 命令。进入如下页面。

   ![image-20201101183747923](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101183747923.png)

最上面黄色那行是用来给这次提交作一个备注。

保存并离开后回到如下页面。

![image-20201101183823012](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101183823012.png)

master(root-commit)：表示当前的good.txt文件被添加到根root下。

4 insertions(+)：表示提交的文件中有4行被修改。



5. **提交文件的简便方法：git commit -m "..."** 

   使用该命令就不用进入文件内部去添加此次修改的备注，直接再 -m 后的冒号中添加备注即可。

   为了演示这个命令，同时我们使用 vim good.txt 修改了这个文件。修改这个文件之后，文件的状态如下图

   ![image-20201101190039541](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101190039541.png)

   modified：表示这个文件被修改过。

   ”git add“ and/or ”git commit -a“：正常提交文件时，先添加再提交。但也可以直接用git commit -a 命令直接提交文件到本地库。

我们先添加再提交修改过后的good.txt 文件。

![image-20201101190626136](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101190626136.png)

​	master c295e68 ： 表示此次提交到了第一次提交的good.txt后面。

#### 查看历史记录

命令：

1. git log ：打印迄今为止所有的提交的历史记录，显示详细信息。
2. git log --pretty=oneline：同上，但是每一次提交记录只简短的显示一行
3. git log --oneline：同上，只显示部分哈希值。
4. git log reflog：同上，还会显示到达某一版本需要的步数。

使用 git log reflog ：

![image-20201101204110383](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101204110383.png)

head包括的值就是移动所需要的步数。

HEAD ：表示当前指针所指向的版本，可以理解为索引。

#### 版本的移动

工作中一个文件会经历许多次提交，而每一次提交都会被记录起来，所以我们可以回顾历史提交。

**有3中移动方式：**

1. 基于索引值：利用每个记录的哈希值

   命令： git reset --hard 

   ![image-20201101205628506](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101205628506.png)

   此时HEAD指针指向了哈希值为 f553ac1 的那个版本。

   

2. 基于 ^：只能后退，一个^ 代表后退一次，2个代表两次。

   命令：git reset --hard HEAD

   ![image-20201101210355029](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101210355029.png)

3. 基于 ~：只能后退，用来简化基于 ^ 的后退操作，~2 代表后退两次。

   命令：git reset --hard HEAD

   ![image-20201101210412437](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101210412437.png)

**reset 命令的参数**

1. --soft：仅仅移动本地库的HEAD指针，不会碰暂存区和工作区的HEAD指针。
2. --mixed：在本地库移动HEAD指针，并重置暂存区。
3. --hard：在本地库移动HEAD指针，并重置暂存区和工作区

为了方便记忆，可以想象三条水平线，使用soft参数只会修改本地库的HEAD指针，使用mixed参数还会修改暂存区的HEAD指针，使用hard参数还会修改工作区的HEAD指针。

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101110952853 - 副本.png" alt="image-20201101110952853 - 副本" style="zoom: 50%;" />

#### 永久删除并恢复

删除命令：rm 

恢复的前提： 文件被创建的记录要提交到本地库

**删除一个文件后还是要将该文件添加到暂存区，然后提交到本地库。这样历史记录中才会添加你删除这个文件的记录**

**虽然是永久删除，但还是可以用 git reset --hard HEAD  命令回到删除d.txt文件之前的版本，进而恢复文件**

1. 永久删除d.txt文件

![image-20201101215444730](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101215444730.png)

2. 将d.txt文件添加到暂存区

![image-20201101215554645](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101215554645.png)

3. 将d.txt文件提交本地库，然后可以看到历史记录中存储了对d.txt文件的删除记录

![image-20201101215607329](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101215607329.png)

#### 暂存区删除的恢复

使用rm命令删除了apple.txt文件，但只使用git add命令将它添加到了暂存区，并没有提交到本地仓库时，可以使用**命令：git reset --hard HEAD** 命令将它恢复。原理就是当前HEAD指针指向的是添加apple.txt的那次提交，该命令将本地库，暂存区，工作区三个区的HEAD指针都指向了这次提交，所以apple.txt文件被回复。

#### 比较文件差异

比较工作区的文件和缓存区或者本地库中保存的版本有什么差异。

命令：

1. git diff：将工作区中文件和暂存区保存的该文件版本进行比较。

   ![image-20201102110123222](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102110123222.png)

2. git diff HEAD ：将工作区中文件和本地库中保存的该文件版本比较

   ![image-20201102110142246](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102110142246.png)

   HEAD^： 就是和上一个版本比较

### 分支管理

项目开发过程中各个模块并行开发，提高开发效率。即使某个模块出现重大失误也不会影响整个项目的开发。当某个模块开发好了之后，将该模块并入到主程序中即可。这就是分支的意义。

![image-20201102111019044](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102111019044.png)

#### 命令

1. **创建分支：git branch** 

   ![image-20201102112042396](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102112042396.png)

2. **查看所有分支：git branch -v**

   ![image-20201102112108465](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102112108465.png)

3. **切换当前所在分支：git checkout** 

   ![image-20201102112125196](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102112125196.png)

4. 两个分支合并：git merge

   比如hot_fix分支对apple.txt文件做了修改并提交，此时hot_fix分支比master分支领先一个版本，现在打算将hot_fix分支并入到master分支上。合并之前必须切换到master分支上，也就是说：要把B分支添加到A分支上时，首先要切换到A分支上。

   先切换到master分支上:

   ![image-20201102113217835](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102113217835.png)

   然后将hot_fix分支合并到master 分支上：

   ![image-20201102113259618](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102113259618.png)

#### 合并时发生冲突

一个文件会被不同分支所修改，如果修改了同一个文件的同一行内容的两个分支要合并，那么在两个分支合并时就会发生冲突，此时git不确定要用谁修改过的版本。

- 冲突的表现

  ![image-20201102115247604](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102115247604.png)

  master分支和hot_fix分支都对同一行进行了修改，git用 < ，=== 和 > 符号把冲突内容包裹起来。

- 解决冲突：

  两个分支的开发人员自行协商，将gti添加的特殊符号删掉，将文件修改成合适的内容即可。

  协商并修改过后，将apple.txt文件添加，然后提交。

- 注意：

  将apple.txt文件添加到暂存区之后，文件的状态。

  ![image-20201102120639184](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102120639184.png)

  将apple.txt文件添加到暂存区之后，提交时文件名不能出现。

  ![image-20201102120547595](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102120547595.png)

### SSH免密登陆

推送时不再登陆github账号，可以直接推送。缺点是只能往一个github账号中的仓库推送。

1. 进入当前用户的家目录，命令：cd ~

   删除 .ssh 目录，命令： rm -rvf .ssh

2. 生成 .ssh 密钥目录，命令：$ ssh-keygen -t rsa -C

   ![image-20201104101812421](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104101812421.png)

3. 进入 .ssh 目录查看文件列表，命令： cd .ssh

   然后查看d_rsa.pub 文件内容，并复制内容。

   ![image-20201104101946658](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104101946658.png)

4. 进入github账号的setting --- ssh and gpg keys 中，新建一个ssh keys，将复制的内容粘贴进去。

5. 在本地添加远程库的ssh，以后提交到sshtest代表的仓库时不用登录账号。

   ![image-20201104102323582](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104102323582.png)

## 远程库操作

### 推送文件到远程库

1. 初始化一个本地库。

2. 登陆github，并创建一个远程仓库，复制仓库地址。

3. **在本地存储远程库的访问地址**

   **命令：git remote add** 

   ![image-20201102163845676](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102163845676.png)

   local ： 是给远程库起的别名，方便使用。

4. **查看本地存储的远程库**

   **命令： git remote -v**

   ![image-20201102163955729](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102163955729.png)

   显示出所有的 仓库别名 和 仓库地址。

5. **将本地库的文件push到远程库**

   **命令：git push** 

   ![image-20201102164113180](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102164113180.png)

   local ：远程库的别名。

   master：要推送到的远程库的分支。

   master -> master ：本地库的master推送到远程库的master



### clone远程库到本地

1. 在本地新建一个文件夹clonerepository

2. clone远程库

   命令：git clone 

   ![image-20201102165117885](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102165117885.png)

   clone后面跟的是 远程库的地址。将远程库下载到本地。

3. git帮我们把远程库的属性也克隆过来

   远程库的别名也被复制：

   ![image-20201102165347494](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102165347494.png)

   远程库的初始化：

   ![image-20201102165358188](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102165358188.png)

### 团队内协作

 <img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101111536196.png" alt="image-20201101111536196" style="zoom:80%;" />

1. 远程库的所有者邀请团队内部未加入的人员加入

   这需要在github中仓库的setting选项处邀请

   ![image-20201102165919373](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102165919373.png)

   点击invite 按钮

   ![image-20201102170006029](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102170006029.png)

   输入外部人员的github账号名即可向他发出邀请。并将邀请链接私下里发送给外部人员，外部人员在自己的github页面上打开这个邀请链接，显示下图，点击接受即可加入团队。然后就可以向自己clone的远程库中提交文件了。

   ![image-20201102171657463](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102171657463.png)

#### 远程库修改的拉取

pull 操作可以分为两步：先fetch再merge。直接pull也可以。

1. fetch 远程库

   原理就是将远程库的文件下载到本地。

   **命令：git fetch**

   ![image-20201102175715561](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102175715561.png)

   git fetch 远程库别名  远程库的分支

   

2. 先查看下载的内容，不用急着合并

   **命令：git checkout** 

   此时本地的分支情况为：

   ![image-20201102180107347](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102180107347.png)

   master：本地库的master分支。

   绿色字体的就是下载下来的分支。

   

   切换到远程库的分支上，然后就可以用 cat 命令查看下载下来的文件了。

   注意格式是 远程库别名 / 分支。

   ![image-20201102175831130](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102175831130.png)

   

3. 将文件合并到本地库

   **命令：git merge**

   记得先切换到本地库的分支上，然后合并

   ![image-20201102180600047](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102180600047.png)

   

#### 拉取中的冲突

当团队协作时必定出现冲突，多个开发人员对同一个文件的同一行进行修改后，谁晚一点push到远程库谁就会出现冲突，这是Git的机制。想要解决冲突，那么就要先把别人对远程库的修改pull下来，合并到自己的本地库中，然后再push自己的修改到远程库。对于文件中冲突的部分，开发人员需要自己协商着修改。和前面处理分支合并时的冲突一样。

### 团队间协作

<img src="C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201101112608656.png" alt="image-20201101112608656" style="zoom: 67%;" />

1. 外部人员在自己的github账号中fork组长的远程库，复制fork到的远程库地址
2. clone远程库到本地即可使用
3. 外部人员在自己的github账号中发送pull request请求
4. 组长收到pull request请求，审核后即可合并到自己的远程库中。







# Git基本原理

## 哈希

哈希是一系列的加密算法，不同的加密算法加密程度不同，但都有如下特点：

1. 不管输入量有多少，对于同一个输入，它的哈希值长度固定。
2. 哈希算法确定后，输入不变，那么输出也不会变。反之，输入哪怕有一丁点变化，那么输出也会发生巨大改变。
3. 哈希算法不可逆。

Git底层采用的是SHA-1算法，哈希算法可以用来检验文件的完整性。

比如，本地文件的哈希值已知，现在将本地文件通过网络传送到远程仓库，那么代码托管中心就可以利用它收到的文件的哈希值和本地文件生成的哈希值进行比对，进而确定经过网络传输之后这个文件有没有缺损。

## 保存版本的机制

1. 集中式版本控制工具的文件管理机制：以SVN为代表

   每一个版本只存储每个文件被修改的部分。需要切换到 version3 时，只需要处理 version3之前的文件即可。

   ![image-20201102123249119](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102123249119.png)

   

2. 分布式版本控制工具的文件管理机制：以Git为代表

   version2中 文件B没有被修改，那么它只保存指向上一个版本的文件B的指针即可。

   ![image-20201102123657143](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102123657143.png)



3. Git文件管理机制的细节

   Git的 ”提交对象“：

   ![image-20201102124131947](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102124131947.png)



​	提交对象及其父对象形成的链条：

​	每一个快照中保存着他的父版本的哈希值 和 包含的tree![image-20201102124642680](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102124642680.png)



## 分支管理机制

1. 分支的创建

   testing 分支创建时只是创建了一个指针，并没有将原来的分支复制一份拿来用。

   ![image-20201102160701205](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102160701205.png)



2. 分支的切换

   切换分支时只是将HEAD指针指向了新的分支testing，所以切换分支的速度很快。

   ![image-20201102160839875](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102160839875.png)



​		此时又提交了一个版本也仅仅是testing这个分支的指针移动，而master分支的指		针并没有移动。

![image-20201102161113037](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201102161113037.png)



# Git工作流

在项目开发过程中使用Git的方式。

## 分类

1. 集中式工作流

   ![image-20201104102747741](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104102747741.png)

2. GitFlow工作流

   ![image-20201104103018191](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104103018191.png)

3. Forking 工作流

   建立在GitFlow工作流的基础上，大型项目用得多。

## GitFlow 工作流

![image-20201104103411879](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104103411879.png)

## 分支实战



![image-20201104104806750](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104104806750.png)



# idea中使用Git

## 配置

1. 配置git 和 github 账号

2. 创建本地库

   点击下面的按钮后，在窗口中选择为哪个工作路径创建本地库。

   ![image-20201104112535758](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104112535758.png)

   ![image-20201104112701505](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104112701505.png)

   创建后项目的显示如图，红色的文件表示还未添加到暂存区。

   ![image-20201104113958162](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104113958162.png)

3. 设置排除文件

   有的文件不需要被提交到远程库中，所以需要git识别出来。

   右键项目进入git，在.git文件的info目录中添加exclude文件，需要忽略什么文件就在exclude文件中设置。

   ![image-20201104113649099](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104113649099.png)

   

   exclude文件中设置忽略以 .iml 结尾的文件。

   ![image-20201104114150428](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104114150428.png)

   

   idea的项目目录中可以看到 .iml 结尾的文件变成灰色。

   ![image-20201104114237151](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104114237151.png)



## 操作

为当前模块创建本地库后，idea工具栏显示git的快捷操作按钮。

![image-20201104115031735](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104115031735.png)

### add 和 commit 操作

也可以采用按钮的方式，但它不能添加提交备注。

![image-20201104114433164](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104114433164.png)

### 切换版本

当前提交了四个版本，如下。

![image-20201104120036952](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104120036952.png)

假如我们想要回到commit 2。右键commit2并复制它的标识。

![image-20201104120234339](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104120234339.png)

右键模块然后选择，跳转到目的版本。

![image-20201104120417534](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104120417534.png)

输入标识后，即可跳转。缺点是只能往回跳，往前跳需要手动。

![image-20201104120517085](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104120517085.png)



### 创建和合并分支

1. 创建分支

   右键模块，显示如下。

   ![image-20201104125146188](C:\Users\xpty\AppData\Roaming\Typora\typora-user-images\image-20201104125146188.png)

   Branches：用来新建分支和切换分支

   Merge：用来合并分支，前提是切换到你要将分支合并到的分支

### 解决冲突

和使用命令行时解决冲突思路一样。

### 推送到远程库

还是上图。

Push：用来设置远程库的地址

### 更新本地库

远程库被其他人修改了，本地库落后了若干版本。此时要将本地库推送到远处库会出错，需要merge远程库，或者rebase远程库。

merge：将远程库领先的所有版本都合并到本地库。

rebase：只将远程库中最新的版本合并到本地库

### 克隆远程库

VCS--->Git---->clone ，输入远程库的地址即可。