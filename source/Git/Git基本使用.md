# Git 基本使用


### Git中的三个分区：
1. 工作区 - Working Directory
2. 暂存区 - Staging Area
3. 仓库 - Responstory

### Git的三种状态
- 已提交（committed）
- 已修改（modifyed） 
- 已暂存 (staged) 

我们可以从仓库中checkout出特定的分支内容，放到工作区中，待我们将代码修改完成，即可提交到暂存区中(git add)，暂存区本质是一个文件，保存了下次将要提交的文件列表。最后我们将修改的内容一次提交到本地仓库中（git commit）。git仓库是存储元数据和对象的地方，当我们Clone 时，就是复制的数据。

----

### Git文件系统

![dyTYHP](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/dyTYHP.png)

## Config
Config文件有三个，分别对应三种不同的指令
- **etc/gitconfig**  :  
    `git config --system `修改的就是这个目录下的配置信息
    他是本系统上每个用户的通用配置

- **~/gitconfig**  :  
    `git config --global`用户级别的配置修改，只针对当前用户有效

- **.git/**  :  
    `git config --local`修改本地目录下的内容
他们的优先级是依次递增的。

##### 相关配置查看方式
``` 
 git config --global -l 查看当前用户配置
 git config --system -l 查看系统配置
 git config -l 查看所有配置
 ```

##### 常见配置字段
- `user.name` ： 用户名
- `user.email`： 邮箱
- `core.edit` ： 基本编辑器
- `commit.template` ： 设置commit信息模板
- `core.excludesfile` : 指定一个包含有不要进入git的文件名信息的文件。和gitignore 作用一样。
- `color.ui` ： 所有操作是否显示颜色
- `color.branch`：指定branch是否显示颜色
- `receive.fsckObjects` ： 是否强制每次在和服务器交换数据时都检查文件一致性，设置为true是，每次都强制检查，比较耗时。
- `receive.Deletes` ： 设置不允许用户删除服务器上的分支。ture为不允许


---

## Objects
##### 数据对象

- 我们可以用find指令,来显示git object的存储内容
![IqDsAK](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/IqDsAK.jpg)

我可以通过`git hash-object -w --stdin`向git对象中存储一条记录
![lIwxfM](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/lIwxfM.jpg)
存储后的记录效果是这样的
![8vUZ1p](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/8vUZ1p.jpg)

我们还可以通过`git cat-file -p`去读取这条记录内容
![MNwb59](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/MNwb59.jpg)

这里每条内容对应一个文件,通过这个内容再加上特定的头部信息的SHA-1 校验和为文件命名.其中校验和的前两个字符用于命名子目录,余下的字符作为记录文件名

至此,我们需要知道,我们存储的仅仅是文件内容,但是文件名并没有被记录,我们称这种数据类型的对象为 **数据对象 blob**
我们可以通过`git cat-file -t`来判断对应的数据类型

##### 树对象
用于处理文件名,树对象对应了UNIX中的目录.一个树对象包含了一条或多条树对象记录.每条记录包含了一个指向**数据对象**或**子树对象**的`指针`,已经相应的模式、类型、文件名信息.
![YAenMN](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/YAenMN.jpg)
我们可以通过`git cat-file -p`获取对象类型,而其中的`master^{tree}`表示master 分之上最新条所指向的树对象

通过情况下,一个树对象是通过暂存区表示的状态创建的.因此树对象出现的前提是暂存区的文件.

##### 提交对象
用于保存树对象,也就是不同项目版本的快照.换句话说说,每个提交对象都会对应一个树对象,同时他也保存了一些提交的基本信息(作者、时间、注释)

##### 包对象

git会时不时的将对象打包成一个叫packfile的二进制文件,以节省空间.
### 对象存储方式
1. Git首先构造对象类型作为开头构造头部信息,然后在头部的第一部分添加空格,紧跟着是数据内容的字节数,最后是一个空字节

```blob #{content.length}\0```

 2. git将头部信息和原始数据拼接,并计算这条内容的SHA-1校验和.

 3. git通过zlib压缩内容,并写入磁盘.


所有的git对象都是以这种方式存储,却别在于类型标识不同.



## refs
git分支的本质是一个指向某一些提交之首的指针或引用.
 - HEAD引用 :  是一个符号引用,指向所在的分支,是一个指针,但是在特殊情况下(如检出一个tag、提交、或远程分支)就会出现这种情况

![zPST9Y](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/zPST9Y.jpg)

- 标签引用 : 除了数据对象、树对象、提交对象还有标签对象.他包含了一个标签的创建者信息、日期、注释和指针.标签对象通常指向一个提交对象,而不是一个树对象.

- 远程引用 : 如果你添加了一个远程版本,并执行了推送操作.git会记录下最近一次推送操作时每个分支所对应的值,保存在`refs/remotes`目录下

其中远程引用和分支的区别在于,远程引用是只读的,git将这些远程引用作为远程服务器珊瑚各个分支最后已知位置状态的书签.换句话说,通过远程分支,我们知道当前本地各个分支和远程对应的各个分支是否处于同一个提交节点上.




##  hooks
包含了客户端和服务端的hook脚本

## Description
仅仅是GitWeb程序使用

## HEAD
HEAD 文件里记录的是指向当前分支上最新提交的符号引用.
但是前面也提到了,在`refs/head`目录下,也存储着指向当前最新条的指针
当我们指向`git commit` 时,对应refs文件夹下的head会存储最新的提交对象. 换句话说, HEAD指向的是分支,head指向的是提交

---

####  origin 是什么 ?
origin 是当你运行clone 时,默认的远程仓库的名字.

---

### 基本工作流

### 代码拉取
![PIold4](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/PIold4.jpg)


###  当前状态
![1qUcOl](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/1qUcOl.jpg)
我们可以看到在Unchacked list下有一个README.md文件。这意味着这个文件没有被存为快照，不在git的track范围内。我们可以通过git addd 将其添加到追踪范围内
![ZUr5hr](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/ZUr5hr.jpg)
这样，我们就将已修改的文件放入了暂存区。



### 提交到暂存区
![eq9lpm](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/eq9lpm.jpg)


### Git代码管理


## 参考文献
- [Git config 设置 Git 参数](https://wolfsonliu.github.io/archive/2018/git-config-she-zhi-git-can-shu.html)