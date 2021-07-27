# 重点

## 1. *版本回滚——git reset*

https://blog.csdn.net/weixin_44802825/article/details/104814984

### 原理

**working tree  ----add---->  暂存区  ----commit---->  本地仓库 ----push----> 远程仓库**

[reset加不加hard的区别](https://blog.csdn.net/chenpuzhen/article/details/92006378)

> reset --soft——仅仅将HEAD指向新版本号
>
> reset——将HEAD指向新版本号，且更改暂存区（reset --mixed是默认情况，--mixed可省略）
>
> reset --hard——将HEAD指向新版本号，且暂存区和工作区一起更改

> https://blog.csdn.net/albertsh/article/details/106448035
>
> HEAD——指向当前分支的最新的commit
>
> HEAD^和HEAD~
>
> ![image-20210726150257425](D:\WorkSpace\工作笔记\Git\Git使用补充.assets\image-20210726150257425-1627282981520.png)
>
> ![image-20210726150413658](D:\WorkSpace\工作笔记\Git\Git使用补充.assets\image-20210726150413658-1627283055489.png)
>
> HEAD~默认是HEAD^

> ### 已add未commit
>
> git reset HEAD就是回退到当前版本——用本地仓库还原暂存区
>
> add以后我们发现工作区中添加了错误的内容并且add了，此时我们只是做了add 操作，就是将修改了内容添加到了暂存区，**还没有执行commit，所以还没有生成版本号，当前的版本号对应的内容**，还是你add之前的内容，所以我们只需要将代码回退到当前版本就行。 
>
> 其实到这里，暂存区的修改就撤销了。工作区中内容可用下面的方法撤销，但是没什么必要，直接修改就行了。除非，**你需要一步将Workspace中某些文件还原**。
>
> （执行**git reset HEAD**后，***用git status查看，发现已经退到未add的状态（Workspace中有未track的文件）。***这表明暂存区已经被还原到add之前的状态。但是Workspace中有未track的文件恰恰表明，Workspace中的错误文件还在。要想**回退Workspace中的文件的错误修改**的话：**it checkout --<file> to discard changes in working directory**这个意思就是下载某某文件，**丢弃掉该文件在工作区的改变内容** ，需要根据提示输入被修改的文件名。）
>
> 这样，先撤销add到暂存区，再撤销对Workspace某个文件的修改，最后完全撤销对文件的错误修改。
>
> ### 已commit
>
> 已经commit了，还没有push,push的内容我们先不管，push这个命令其实和提交没关系，他只是推送到远程了，如果push了，也就是我们**回退了之后，再重新push一下而已**，所以请不要纠结push这个操作。他和提交版本其实没有关系的。 
>
> 已经commit了，说明已经生成了最新的版本号了，此时我们想回退，则肯定是回退到之前的一个版本，版本号用git log查看。 git为我们提供了一个更简单的回退上一个版本的方法 **git reset HEAD^**,此命令专门用于回退到上一个版本。若你的错误已经经过好几次commit，回退到上一个版本无法解决时，就查看版本号，用git reset 版本号回退。
>
> 回退后，就进入了（1）中 的状态，再按照（1）中描述进行回退。
>
> ### 关于git reset --hard——用本地仓库还原暂存区+工作区
>
> 用来撤销已add未commit。
>
> 该指令和git reset HEAD+ it checkout --<file> to discard changes in working directory两步相同，一步到位直接将暂存区和Workspace都回退到本地仓库中的当前版本。

### 日常使用情景

> * 未commit，未生成新的版本号
>
>   用本地仓库**回滚暂存区**，将暂存区和HEAD保持一致
>
>   本地仓库的最新commit不变，因为HEAD的指向未变
>
>   ```
>   git reset HEAD
>   ```
>
> * 未commit，未生成新的版本号。用本地仓库回滚**暂存区和WorkSpace**，将工作区、暂存区和HEAD保持一致
>
>   ```
>   git reset --hard HEAD
>   ```
>
> * 已commit，生成了新的版本号，回滚本地仓库到上一版本号
>
>   HEAD指向上个commit
>
>   ```
>   git reset HEAD^
>   git reset 版本号
>   ```
>
> * 已commit了，生成了新的版本号，回滚本地仓库到上一版本的同时，将工作区和暂存区也于新的HEAD保持一致
>
>   HEAD指向上个commit
>
>   ```
>   git reset --hard HEAD^
>   git reset --hard 版本号
>   ```

## 2 远程仓库回滚

当代码已经Push到远程仓库后，发现push的代码有问题，怎么回滚到之前的版本呢？



## 3. *分支操作*

**分支操作的各种情形下的处理方法，最好的办法是明确提交路径（commit快照）**

**本地电脑上不但有本地的分支HEAD，也会记录远程仓库的指针origin/master和origin/dev，push时和远程仓库同步**

https://www.cnblogs.com/andydao/p/6808431.html

### （1）新建分支

commit后才会新建分支master，这之后才可以新建其他分支

```
git branch daily/0.0.0	//新建日常开发分支daily/0.0.0
```

![image-20210726093541204](D:\WorkSpace\工作笔记\Git\Git使用补充.assets\image-20210726093541204-1627263344263.png)

### （2）重命名分支

```
git branch daily/0.0.0 daily/1.0.0	//重命名分支daily/0.0.0为daily/1.0.0
```

### （3）删除分支

* 删除本地分支daily/1.0.0

  ```
  git branch -d daily/1.0.0	
  ```

* 强制删除本地分支

  ```
  git branch -D daily/1.0.0	
  ```

* 删除远程分支**(慎用)**

  ```
  git push origin --delete daily/1.0.0
  ```

### （4）查看分支列表——git branch -a

```
git branch	//当前项目分支列表
```

```
git branch -a	//查看本地仓库和远程仓库上所有分支列表

git branch -r	//查看远程仓库所有分支列表
git branch -r -d origin/branch-name		//查看并删除远程仓库上分支branch-name
git branch -D	//分支未提交到本地版本库前强制删除分支
bit branch -vv	//查看本地仓库分支列表，带有各分支的最后提交id、提交原因
```

### （5）切换分支——git checkout

切换分支，HEAD会改变指向

```
git checkout daily/1.0.0
```

创建 `daily/0.0.1` 分支，同时切换到这个新创建的分支

```
git checkout -b daily/0.0.1
```

创建dev分支的同时，关联远程分支origin/dev

```
git checkout -b dev origin/dev
```







### （11）暂存——git stash



### （12）分支的日常使用情景



## 4. 冲突操作

### （1）git clone

git clone拉取远程仓库代码，自动创建远端仓库指针origin/master指向远端仓库的最新commit，同时创建本地指针master同origin/master指向同一commit

![img](https://i.loli.net/2021/07/27/Bg8AVNES1LYtIZR.jpg)



### （2）git fetch

https://blog.csdn.net/qq_42780289/article/details/98049574

https://www.jianshu.com/p/d07f5a8f604d

#### fetch仅到本地仓库，不到暂存区和working tree

![img](https://i.loli.net/2021/07/27/kNTbjcUVmPa2JK5.jpg)



#### git fetch [远程主机名] [分支名]

* 指定远程仓库，默认是master分支

  ```
  git fetch [远程主机名]
  ```

* 取回特定分支

  ```
  git fetch [远程主机名] [分支名]
  ```

  比如，取回origin仓库的master分支

  ```
  git fetch origin master
  ```

* 取回的代码在本地主机上会建立指针`远程主机名/分支名`指向拉取的分支的最新commit

* git fetch命令通常用来查看其他人的进程，因为它**取回的代码对你本地的开发代码没有影响**

  对于拉取的代码分支，查看后，若不需要，可用`git branch -d`删除



#### git fetch origin

从远程仓库origin抓取origin/master分支的代码和提交，origin/master指向远端仓库的最新的commit，和本地代码master形成分支

![img](https://i.loli.net/2021/07/27/MRdiZWymbrCh9LH.jpg)



* 拉取了origin/master后，此时由于C0和D0，origin/master和master产生了分支。

  此时，1. 可以在origin/master的基础上工作。2. 可以将origin/master合并到master分支

  > 在origin/master基础上工作，而不是在master基础上工作的话。建立本地分支dev关联远程分支origin/master
  >
  > ```
  > git checkout -b dev origin/master
  > ```
  >
  > 
  >
  > ![img](https://i.loli.net/2021/07/27/fGwY4utAS1Vd98a.jpg)

> 将origin/master分支的内容和master分支合并
>
> ```
> //直接将origin/master合并到master分支
> git checkout master
> git merge origin/master
> ```
>
> ![img](https://i.loli.net/2021/07/27/oXAmFwKq7pNinSg.jpg)





### （3）git merge

#### [图解说明，豁然开朗](https://zhuanlan.zhihu.com/p/132573100)

https://www.jianshu.com/p/58a166f24c81

https://zhuanlan.zhihu.com/p/269043827

```
git merge [待合并分支]	//将待合并分支合并到当前分支
```

#### fast-forward模式



#### no-fast-forward模式

```
git merge --no-ff
```





### （4）rebase

https://zhuanlan.zhihu.com/p/145037478

https://www.jianshu.com/p/4a8f4af4e803

https://blog.csdn.net/nrsc272420199/article/details/85555911

**不要通过rebase对任何已经提交到公共仓库中的commit进行修改，只针对自己的开发分支进行。**

rebase主要有两个用法：

#### 合并多个commit为一个完整commit

***这种用法有一个重要问题：rebase后可以push吗？？***

> 

> 当我们在本地仓库中提交了多次，在我们把本地提交push到公共仓库中之前，为了让提交记录更简洁明了，我们希望把如下分支B、C、D三个提交记录合并为一个完整的提交，然后再push到公共仓库。
>
> ![image-20210727005742741](https://i.loli.net/2021/07/27/p2Z9LfvRxYdCT6U.png)
>
> 现在我们在测试分支上添加了四次提交，我们的目标是把最后三个提交合并为一个提交：
>
> ![image-20210727005804171](https://i.loli.net/2021/07/27/l4RjwYCfUtPIeSF.png)
>
> 这里我们使用命令:
>
> 
>
> ```css
> git rebase -i  [startpoint]  [endpoint]
> ```
>
> 其中`-i`的意思是`--interactive`，即弹出交互式的界面让用户编辑完成合并操作，`[startpoint]`  `[endpoint]`则指定了一个编辑区间，如果不指定`[endpoint]`，则该区间的终点默认是当前分支`HEAD`所指向的`commit`(注：该区间指定的是一个前开后闭的区间)。
> 在查看到了log日志后，我们运行以下命令：
>
> 
>
> ```undefined
> git rebase -i 36224db
> ```
>
> 或:
>
> 
>
> ```undefined
> git rebase -i HEAD~3 //将包含HEAD在内的n个commit合并的话，就是HEAD~n
> ```
>
> 然后我们会看到如下界面:
>
> ![image-20210727005822758](https://i.loli.net/2021/07/27/cI5fMYpBDqr26W4.png)
>
>
> 上面未被注释的部分列出的是我们本次rebase操作包含的所有提交，下面注释部分是git为我们提供的命令说明。每一个commit id 前面的`pick`表示指令类型，git 为我们提供了以下几个命令:
>
> > - pick：保留该commit（缩写:p）
> > - reword：保留该commit，但我需要修改该commit的注释（缩写:r）
> > - edit：保留该commit, 但我要停下来修改该提交(不仅仅修改注释)（缩写:e）
> > - squash：将该commit和前一个commit合并（缩写:s）
> > - fixup：将该commit和前一个commit合并，但我不要保留该提交的注释信息（缩写:f）
> > - exec：执行shell命令（缩写:x）
> > - drop：我要丢弃该commit（缩写:d）
>
> 根据我们的需求，我们将commit内容编辑如下:
>
> ![img](https:////upload-images.jianshu.io/upload_images/2147642-a651234e62ed20a5.png?imageMogr2/auto-orient/strip|imageView2/2/w/536/format/webp)
>
> 然后是注释修改界面:
>
> **最终保留的是哪个commit，就修改哪个commit的message**
>
> ![image-20210727005838589](https://i.loli.net/2021/07/27/PCRjnJEeOoy98cr.png)
>
> 编辑完保存即可完成commit的合并了：
>
> ![image-20210727005904255](https://i.loli.net/2021/07/27/RmnLdGhNcgQHTPJ.png)

#### 将某一段commit粘贴到另一个分支上

> 当我们项目中存在多个分支，有时候我们需要将某一个分支中的一段提交同时应用到其他分支中，就像下图：
>
> ![image-20210727012338240](https://i.loli.net/2021/07/27/MNLszwOUFVteBro.png)

### （5）merge和rebase用哪个？

* 说法一

  不要用merge，用rebase，https://zhuanlan.zhihu.com/p/75499871

* 说法二

  https://zhuanlan.zhihu.com/p/57872388

  merge和rebase都要用，rebase会隐藏分支的提交历史，最终呈现的结果只有master上的若干分支合并历史，而分支上的commit历史则会消失。




### （6）git pull=git fetch +git merge

https://blog.csdn.net/weixin_41975655/article/details/82887273

![image-20210726171937862](D:\WorkSpace\工作笔记\Git\Git使用补充.assets\image-20210726171937862-1627291178971.png)

---

# Git日常使用

## 1. 使用已有的rsa私钥在另一台电脑上使用已有远程仓库

设置用户名： git config --global user.name wk

设置密码： git config --global user.password p

将私钥复制到~/.ssh文件夹下：C:\Users\Administrator\.ssh

身份验证： ssh -T git@github.com 

验证成功：Hi 用户名! You've successfully authenticated, but Github does not provide shell access.

将远程仓库clone下来，就可以提交修改了（clone别人的代码修改后无法提交，需要别人同意）

## 2. gitk——启动图形查看模式



## 3. 修改上次commit的message——git commit --amend

```
git commit --amend
```

合并暂存区的修改和HEAD的commit，然后生成新的commit（**commit ID变化了**），用新的commit替代原来的commit。若暂存区没有修改，就相当于替换commit ID和重写message。

#### 功能1——修改message

```
git commit --amend
```

#### 功能2——修改上次commit

> ```
> //做了一些修改
> git add .
> git commit --amend --no-edit	//--no-edit表示不重新编辑message
> ```
>
> 其实也是可以用git reset方法
>
> ```
> git reset --hard HEAD~	//用HEAD~还原本地仓库、暂存区、working tree
> //修改
> git commit -m "重新提交"
> ```

## 3. git push时会更新本地的origin/分支的指向

## 3. HEAD是什么？HEAD^/HEAD~/HEAD~2/HEAD^2是什么？Origin是什么？master是什么？Branch是什么？git push -u中的u是什么？

https://www.zhihu.com/question/20019419

* Branch——分支

* Origin——`origin` 指代的是当前的git服务器地址

* master——`master` 是 `Git` 为我们自动创建的第一个分支，也叫主分支，其它分支开发完成后都要合并到 `master`

* [HEAD](https://www.zsythink.net/archives/3412/)——指向**当前分支**的**最新的提交。作用很像是数据结构中指向二叉树根节点`root`的指针。有个 `root` 指针我们就可以对二叉树进行任意操作，它是二叉树的根基。**而 `git` 中的 `HEAD` 概念也类似一个指针，它指向是当前分支的“头”，通过这个头节点可以追寻到当前分支之前的所有提交记录。git 的提交记录之间的关系很像一棵树，或者说是一张图，通过当前的提交记录指向上一次提交记录串联起来，形成一个头结构，而在 git 中我们常常说的切换分支，只不过是 git 客户端帮你把要操作的那条路径的头节点，存储到了 HEAD 文件中。

  HEAD 在 git 版本控制中代表头节点，也就是分支的最后一次提交，同时也是一个文件，通常在版本库中 repository/.git/HEAD，其中保存的一般是 ref: refs/heads/master 这种分支的名字，而本质上就是指向一次提交的 hash 值，一般长成这个样子 ce11d9be5cc7007995b607fb12285a43cd03154b。

* HEAD^2/HEAD~2

  https://blog.csdn.net/albertsh/article/details/106448035

  https://www.cnblogs.com/mengff/p/12809911.html

  https://www.cnblogs.com/chjbbs/p/6418339.html

* git push -u中u——使用一次`git push -u origin master`后，告知Git记忆相关参数，下次只需要`git push`即可。（用-u（--up-stream）来建立本地分支与远程某个分支的关联，形成一个管道，之后 git push可以直接沿着管道 到达关联的分支 无需在加-u参数了）



## 4. 多次commit，想把这若干commit合并为一个——git rebase

当我们开发一个功能，不是一时半会可以完成的时候，为了保护代码不丢失，通常会把这次的修改先 commit，等到这个功能完全做好，再 push。不过这样一来，就会有很多零碎的 commit 记录，这会使远程的提交历史显得杂乱。

必要的时候，我们需要将这些相近的 commit 合并为一个 commit 再 push。当然了，如果你想合并远程的 commit 也是可以的，但请一定要提前跟团队里的其他人说一声，免得有人也在跟你做同样的事情，导致没必要的代码冲突（所以合并 commit 尽量在 push 前）。

https://blog.csdn.net/yinchuan_111/article/details/106913632

https://www.jianshu.com/p/571153f5daa1

```
git log --oneline	//可以更加简明的展示commit日志，且显示的commit-id是简化形式
```

```
git rebase -i commit-id	//commit-id为要最终合并的commit-id的上一条commit。执行完后，会从下条commit开始显示
```

```
修改除第一条commit-id（想要保留的commit）以外的下方若干commit，commit从上往下是逐渐接近最新提交的，最下方的是最新的commit
将除第一条以外的pick改为squash/s，然后:wq保存退出
成功的话，会出现编辑合并后commit的message。编辑新的message，然后:wq保存退出
```

#### 此时会遇到不能push

因为squash和变基，将几个commit合成了一个，而且**生成了新的commit id**。此时本地分支和远端分支就在新commit id处和远端产生了分支

本地master需要fetch+merge远端分支，才可以push。

**1. 不要随便commit，产生多次commit后push。**

**2. 尽量使用`git commit --amend --no-edit`**

![29cb8a83195ae264329f435aa62dba2](https://i.loli.net/2021/07/28/vJie9AuZHNklWpC.jpg)



## 5. 查看文件的修改情况

https://blog.csdn.net/cnds123321/article/details/110731428

* git status

  git status 命令用于**显示工作目录和暂存区**的状态。使用此命令能看到那些修改被暂存到了, 哪些没有, 哪些文件没有被Git tracked到。`git status`不显示已经`commit`到项目历史中去的信息。看项目历史的信息要使用`git log`. 

  **总的来说：红色的是没有track的，需要add到暂存区。绿色的是已经track的。new file/modified/deleted显示文件是新建/修改/删除。根据提示to be commited表明是否commit。总的来说，就是是否track和是否commit。**

> a)    已暂存、未提交 （changes to be committed）——红色
>
> new file //表示新建文件，待commit
>
> modified //表示修改文件
>
> deleted //表示删除文件
>
> b)    已修改（changed but not updated）——绿色
>
> modified //表示修改文件
>
> deleted //表示删除文件
>
> 另外，git 给出了可能需要的操作命令，git add/rm, gitcheckout --
>
> c)    未跟踪 （untracked files）
>
> 没有用add添加到暂存区的文件

* git status -s

  以更简单的形式显示状态。

  - ??标记：**新添加的未跟踪文件**前面会有红色的??标记，即没有执行git add .命令的文件。
  - A标记：**新添加到暂存区的文件**前面会有绿色的A标记，即已经执行了git add .命令的文件。
  - AM标记：A表示该文件**加入到暂存区了，而M表示该文件被修改过了，修改后的文件还没有添加到暂存区**。

* git log

  查看commit的日志

*  git commit --amend 

  功能1：会出现一个编辑器，然后可以修改上一次的提交信息，按键盘上的Insert键进行插入，修改完成后按Esc键并输入:wq保存退出 

## 6.日常操作流程

### （1）直接从别人的项目里拷贝到文件夹里

无需初始化仓库，直接在文件夹里就可以git clone。克隆下来的代码，修改要想提交到远程，必须向原作者发起pr，原作者merge后才可合并到远程仓库。

> git clone `ssh`

### （2）从自己的远程仓库里拉取代码到本地新建的空文件夹

> git init	//建立本地仓库
>
> git remote add origin `ssh`	//连接远程仓库
>
> git pull origin master //拉取远程仓库代码

https://www.runoob.com/git/git-pull.html

**git pull** 其实就是 **git fetch** 和 **git merge FETCH_HEAD** 的简写。 命令格式如下：

```git
git pull <远程主机名> <远程分支名>:<本地分支名>
```

将远程主机 origin 的 master 分支拉取过来，与本地的 brantest 分支合并。

```
git pull origin master:brantest
```

如果远程分支是与当前分支合并（拉取远程仓库代码），则冒号后面的部分可以省略。

```git
git pull origin master
```

### （3）将服务器上的最新代码拉取到本地文件夹（已是项目文件夹，有.git文件夹）

> git pull origin master

**若本地代码被修改，但是未push到远程服务器。且在另一个主机上修改了服务器上代码。那么pull时，服务器上的改动和本地改动就会产生冲突。一般，git会自动合并冲突。但若涉及到同一行代码的改动，就需要手动合并代码**





### 







## 10. push时的忽略文件

```
touch .gitignore	//创建.gitignore文件，编辑该文件，每行代表push时忽略不上传的文件
```

```
//.gitignore
1.md	//忽略1.md文件
build/	//忽略build目录下的所有文件
```

## 11. tag

当完成某项需求时，需要将代码打上一个版本tag，并push到线上。

标签是用于标记特定的点或提交的历史，通常会用来标记**发布版本的名称或版本号**（如：`publish/0.0.1`），虽然标签看起来有点像分支，但打上标签的提交是**固定**的，不能随意的改动。

```
git tag publish/0.0.1	//给当前代码打上标签publish/0.0.1，表示当前代码以publish/0.0.1发布
git push origin public/0.0.1	//推送到服务器
```

## 12. git bash快捷键

* windows系统，在工作目录下，shift+F10，再按s，再按enter。

* 复制：ctrl+shift+c

* 粘贴：ctrl+shift+v

  复制和黏贴快捷键需要按下图设置后才能使用

  ![image-20210718000339131](https://i.loli.net/2021/07/18/kVvGAqnbdtFgOiW.png)

---

# Git高级功能

## 1. git add



## 2. git commit

### git commit -m "" -m ""——多行提交原因

但是这种方式只能写一行的注释，如果你想要对commit的内容进行详细的讲解，以便仔细检查提交的文件，那你可能需要写多行注释，这个命令就不适用了。

```
git  -m "commit title" -m "commit description"
```

![image-20210726104439361](D:\WorkSpace\工作笔记\Git\Git使用补充.assets\image-20210726104439361-1627267481057.png)

![image-20210726104508385](D:\WorkSpace\工作笔记\Git\Git使用补充.assets\image-20210726104508385-1627267509742.png)

### git commit --amend——修改最新一条提交记录的提交原因

```
git commit --amend -m "提交原因"
```

## 3. git status

### 简短方式查看status

```
git status -s
```

![image-20210726105245260](D:\WorkSpace\工作笔记\Git\Git使用补充.assets\image-20210726105245260-1627267966494.png)
