# Git 基本使用


## 1.基本概念

### 1.1 Git中的三个分区：
1. 工作区 - Working Directory
2. 暂存区 - Staging Area
3. 仓库 - Responstory

### 1.2 Git的三种状态
- 已提交（committed）
- 已修改（modifyed） 
- 已暂存 (staged) 

我们可以从仓库中checkout出特定的分支内容，放到工作区中，待我们将代码修改完成，即可提交到暂存区中(git add)，暂存区本质是一个文件，保存了下次将要提交的文件列表。最后我们将修改的内容一次提交到本地仓库中（git commit）。git仓库是存储元数据和对象的地方，当我们Clone 时，就是复制的数据。

----

### 1.3 Git文件系统

![dyTYHP](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/dyTYHP.png)

#### Config
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

#### Objects
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




#### refs
git分支的本质是一个指向某一些提交之首的指针或引用.
 - HEAD引用 :  是一个符号引用,指向所在的分支,是一个指针,但是在特殊情况下(如检出一个tag、提交、或远程分支)就会出现这种情况

![zPST9Y](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/zPST9Y.jpg)

- 标签引用 : 除了数据对象、树对象、提交对象还有标签对象.他包含了一个标签的创建者信息、日期、注释和指针.标签对象通常指向一个提交对象,而不是一个树对象.

- 远程引用 : 如果你添加了一个远程版本,并执行了推送操作.git会记录下最近一次推送操作时每个分支所对应的值,保存在`refs/remotes`目录下

其中远程引用和分支的区别在于,远程引用是只读的,git将这些远程引用作为远程服务器珊瑚各个分支最后已知位置状态的书签.换句话说,通过远程分支,我们知道当前本地各个分支和远程对应的各个分支是否处于同一个提交节点上.


####  hooks
包含了客户端和服务端的hook脚本

#### Description
仅仅是GitWeb程序使用

#### HEAD
HEAD 文件里记录的是指向当前分支上最新提交的符号引用.
但是前面也提到了,在`refs/head`目录下,也存储着指向当前最新条的指针
当我们指向`git commit` 时,对应refs文件夹下的head会存储最新的提交对象. 换句话说, HEAD指向的是分支,head指向的是提交


#### 对象存储方式
1. Git首先构造对象类型作为开头构造头部信息,然后在头部的第一部分添加空格,紧跟着是数据内容的字节数,最后是一个空字节

```blob #{content.length}\0```

 2. git将头部信息和原始数据拼接,并计算这条内容的SHA-1校验和.

 3. git通过zlib压缩内容,并写入磁盘.


所有的git对象都是以这种方式存储,却别在于类型标识不同.


---

### 总结
1. git底层包含四种种对象: 
    - 数据对象 (blob)
    - 树对象 (tree object)
    - 提交对象 (commit object)
    - 标签对象 (tag object) 
2. 对象的存储是将头部信息和数据信息拼接在一起,然后计算出sha-1值,用于存储.
其中,头部信息数据格式是这样的 : `类型 #{content.length} \0`.如 `blob 16\u0000` . 每个对象都会这样的一个头部信息,区别只是类型不同
3. 存储的数据结构长这样
![CTTQkC](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/CTTQkC.png)
横向看,git对象存储主要是以`提交对象`为主要节点进行组织,并用`树对象`存储指向新提交的文件版本和旧文件版本.


!> origin 是什么 ?
   origin 是当你运行clone 时,默认的远程仓库的名字.

---

## 2.基本操作


### 2.1 创建本地git仓库
![PIold4](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/PIold4.jpg)


### 2.2 用户配置
- 配置用户信息 : 在提交代码时,会需要我们事先配置的邮箱和用户名.但是对于不同的项目代码,我们可能需要设置不同的用户名和邮箱.针对这一点,在上文的config介绍中我们已经了解过相关的配置方法.我们只需使用
`git config --local xxx sss@email.com`
`git config --global xxx sss@email.com`
`git config --system xxx sss@email.com`

- 确认配置内容 : 我们以使用`git config  --list`来查看当前的git配置

### 2.3 提交到暂存区
![eq9lpm](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/eq9lpm.jpg)

**撤销 add**
- `git restore`: 这个命令用户将未加入到暂存区的改动进行撤销,换句话说,如果编辑了代码后没有 `git add` 操作,那么可以用这个命令将改动撤销;
    这个命令还有个`-- stage`参数,功能是将文件从暂存区中撤出,但是文件改动不会变化.

![pdTtDT](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/pdTtDT.jpg)


###  2.4 当前状态
![ZhzyGD](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/ZhzyGD.jpg)
我们可以使用`git status` 或者 `gst`来显示当前的提交状态.其中`newb.md`文件时新创建的文件,他还没有被添加到的暂存区中,而`new.md`已经被添加到暂存区中,但是还有被`commit`到仓库中,因此代码仓库的,因此在`Changes not be committed`下会有`new.md`文件.在提交后,我又对`new.md`文件进行了修改,因此在`Changes not staged for commit`一栏下,可以看到的`new.md`文件.并且,git已经在文件名前将对应的操作进行了标记.这样我们就能知道,那些文件是没有被添加到暂存区的,那些文件是的没有被提及到的git仓库的,还有那些是已经被文件已被追踪,但是改动还没有添加到暂存区的.

另外还可以使用`git status -s`或`gss`来简化输出内容
![AVtRpO](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/AVtRpO.jpg)
其中`??`表示该文件未被追踪,`A`表示文件是新添加到暂存区, `M`表示文件已被修改

### 2.5 提交到代码仓库
![Rgsr2L](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/Rgsr2L.png)

将所有的修改加入暂存区之后,我们可以通过 `git commit` 命令进行提交,我们还有可以通过`-a`参数来跳过暂存操作,直接将改动提交到git仓库


**撤销commit**
![3Fl4kx](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/3Fl4kx.jpg)

这里使用`git reset`命令进行撤销,注意这里的`--soft`参数.他仅仅撤销commit,不删除工作区的代码也不会撤销暂存操作
这个命令还有量外两个参数:
- `--hard` : 删除工作去的代码,同时撤销`commit`和`add`提交,此操作会将代码文件删除
- `--mixed` : 不会对工作空间代码进行改动,但是会撤销`commit` 和 `add` 操作,此操作不会删除代码文件.最终效果是将所有文件都重置为unchack状态
![ih4jWg](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/ih4jWg.jpg)

> 小总结 : 我们可以按程度记忆 :
```
 `--soft` < `--mixed` < `--hard`
    |           |           |
commit only     |           |
            commit + add    |
                         commit + add + working + file delete
```


### 2.6 拉取远程代码

#### Fetch 
   `git fetch`操作会拉取远程仓库,但是它不会主动将远程的将改动合并到本地分支中
#### Pull

   `git pull` 会从抓取远程改动并合并到本地代码中

这里需要注意的是,clone代码的时候,默认会将这个仓库添加为远程仓库,并命名为`origin`

### 2.7 提交到远程仓库

#### 添加远程仓库地址
在提交到远程仓库的时候需要确保已经添加远程仓库地址
我们可以使用使用`git remote -v`命令来显示对应的仓库地址.
如果没有地址,我们可以使用`git remote add <shortname> <address>`来指定远程仓库地址和别名

#### Push
`git push orign master` 结合上文,那么这里命令就很容易理解了,这里的origin指代远程仓库.其原本命令是这的 `git push <remote> <local brach>`


### 2.8 分支与标签

#### 标签

我们可以通过标签,来标记一个里程碑式的代码节点,方便后期回溯
- 打标签 :   
    - `git tag <tag name>`
    - `git tag -a <tag name> <hash>` 

- 推送标签 : 
     `git  push`不会将tag提交到远程仓库中,需要我们另外参数指定这个操作,
     - `git push <remote branch> <tag name>` : 指定特定版本推送到远程
     - `git push <remote branch> --tags` : 一次性将所有的tag推送到远程
 - 删除标签
    - 本地标签删除 `git tag -d <tagname>`
    - 远程标签删除 `git push <remote> :refs/tags/<tagname>`.其中冒号前面置为空置并推送到远程,是一种利用命名的形式删除tag的方式
    - 远处标签删除 `git push origin --delete <tagname>` 

- 检出标签 
    - `git checkout <tagname>` : 检出标签后,仓库处于 游离状态(detached HEAD),这种状态下的改动提交,标签本身不会发生变化,并且提交不属于任何分支,必须通过提交的hash才能访问


##### 游离状态(detached) 
一般情况下HEAD指向一个分支(通常是当前分支),当我们使用`git checkout` 检出特定的某次提交之后 (这里需要注意,`git checkout`实际上是修改了HEAD文件的内容,让他指向特定的branch或索引文件),我们就能看到特定提交时的代码状态,我们可以使用这种方式随意切换到我们想要的提交节点上.但是这样,HEAD就处于游离状态,它不属于任何现有的分支,这种情况在`rebase`操作时也会发生.游离状态存在的意思在于你可以查看并在这个状态下进行实验性操作,而不会影响任何分支.

- 如果想抛弃这些改动 : `git checkout  <branch name>`

- 如果想保留在这个状态的改动 : 
    1. `git branch <temp branch name> hashvalue` :  创建临时分支保存commit
    2. `git checkout target target_branch`  : 切换到目标分支
    3. `git merge <temp branch name>` : 合并临时分支
    4. `git branch -d <temp branch name>` : 删除临时分支


#### 分支
   - 显示分支 `git branch`:
     - `-v` : 查看每个分支的最后一次提交记录
   - 创建分支 : `git branch <branch name>`
   - 切换分支 : `git checkout <branch name>`
   - 创建并切换到特定分支 : `git checkout -b <branch name>`
   - 删除分支 : `git branch -d <branch name>`
   - 合并分支 : `git merge <branch name>` 

##### 冲突解决
冲突部分会被`<<<<<<<` 、`========` 、 `>>>>>>>` 符号划分为两个部分,其中上半部分,从 `<<<<<<<` 到 `===`部分是本地的代码,下半部分是拉取下来的代码,可以依据需要保留上半部分或下半部分改动.


##### 变基与合并
    
- 基变(Rebase)
    - 操作流程
      - `git checkout <branch name>`
      - `git reabse master`
      - `git checkout master`
    
- 合并(Merge)
    - 操作流程
      - `git checkout master`
      - `git merge <branch name>`

      
## 参考文献
- [Git config 设置 Git 参数](https://wolfsonliu.github.io/archive/2018/git-config-she-zhi-git-can-shu.html)
- [Git Detached Head: What This Means and How to Recove](https://rollout.io/blog/git-detached-head-what-this-means-and-how-to-recover/)
- [彻底搞懂 Git-Rebase](http://jartto.wang/2018/12/11/git-rebase/)
- [Git team workflow: merge or rebase?](https://ljvmiranda921.github.io/notebook/2018/10/25/git-workflow/)