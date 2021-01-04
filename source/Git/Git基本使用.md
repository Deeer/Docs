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



### Git文件系统

![dyTYHP](https://deeerpictures.oss-cn-beijing.aliyuncs.com/uPic/dyTYHP.png)

##### 1.Config
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

##### 2. hooks
包含了客户端和服务端的hook脚本

##### 3. HEAD


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