# Git使用补充

## 1. 使用已有的rsa私钥在另一台电脑上使用已有远程仓库

设置用户名： git config --global user.name taotaoo 

设置密码： git config --global user.password taotaoo 

将私钥复制到~/.ssh文件夹下：C:\Users\Administrator\.ssh

身份验证： ssh -T git@github.com 

验证成功：Hi 用户名! You've successfully authenticated, but Github does not provide shell access.

将远程仓库clone下来，就可以提交修改了（clone别人的代码修改后无法提交，需要别人同意）

## 2. git commit 时没写 -m 进入vim 模式的处理

进入的是vim模式，按下i进入Insert模式，输入message，在上方以黄色字体显示。按下ESC退出Insert模式，

:wq保存。

## 3. 查看文件的修改情况

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

## 4. 版本回滚

https://blog.csdn.net/chenpuzhen/article/details/92006378

> **Workspace  ----add---->  暂存区  ----commit---->  本地仓库 ----push----> 远程仓库**
>
> **已add未commit：git reset HEAD** 或**git reset --hard**
>
> **已commit：git reset HEAD^或git reset 版本号**



### （1）已add未commit

git reset HEAD就是回退到当前版本。 add以后我们发现工作区中添加了错误的内容并且add了，此时我们只是做了add 操作，就是将修改了内容添加到了暂存区，**还没有执行commit，所以还没有生成版本号，当前的版本号对应的内容**，还是你add之前的内容，所以我们只需要将代码回退到当前版本就行。 

其实到这里，暂存区的修改就撤销了。工作区中内容可用下面的方法撤销，但是没什么必要，直接修改就行了。除非，你需要一步将Workspace中某些文件还原。

（执行**git reset HEAD**后，用git status查看，发现已经退到未add的状态（Workspace中有未track的文件）。这表明暂存区已经被还原到add之前的状态。但是Workspace中有未track的文件恰恰表明，Workspace中的错误文件还在。要想**回退Workspace中的文件的错误修改**的话：**it checkout --<file> to discard changes in working directory**这个意思就是下载某某文件，**丢弃掉该文件在工作区的改变内容** ，需要根据提示输入被修改的文件名。）

这样，先撤销add到暂存区，再撤销对Workspace某个文件的修改，最后完全撤销对文件的错误修改。

### （2）已commit

 已经commit了，还没有push,push的内容我们先不管，push这个命令其实和提交没关系，他只是推送到远程了，如果push了，也就是我们**回退了之后，再重新push一下而已**，所以请不要纠结push这个操作。他和提交版本其实没有关系的。 

已经commit了，说明已经生成了最新的版本号了，此时我们想回退，则肯定是回退到之前的一个版本，版本号用git log查看。 git为我们提供了一个更简单的回退上一个版本的方法 **git reset HEAD^**,此命令专门用于回退到上一个版本。若你的错误已经经过好几次commit，回退到上一个版本无法解决时，就查看版本号，用git reset 版本号回退。

回退后，就进入了（1）中 的状态，再按照（1）中描述进行回退。

### （3）关于git reset --hard

用来撤销已add未commit。

该指令和git reset HEAD+ it checkout --<file> to discard changes in working directory两步相同，一步到位直接将暂存区和Workspace都回退到本地仓库中的当前版本。

但是我不建议大家使用这个，因为这个杀伤力有点强。万一手残删掉了一些自己不想删的就没有后悔药了。所以慎用。

## 5. 当远程仓库的代码已经修改，试图将这种修改拉取到本地

当前文件夹已有.git，也有代码，只是代码落后于远程仓库。

git pull

然后可以对代码进行编辑。编辑结束后git add `flename`、git commit -m "message"、git push filename。

## 6.日常操作流程总结

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

### （3）将远程仓库中对代码的修改拉取到本地文件夹（已是项目文件夹，有.git文件夹）

> git pull origin master

### （4）本地修改代码后的一系列操作

> git add `filename`	//track filename
>
> git status	//查看文件track和commit的情况
>
> [git reset HEAD]	//发现错误，撤销add，手动修改Workspace
>
> [git reset --hard]	//撤销add，也将Workspace的修改撤销，用当前本地仓库的内容覆盖
>
> git commit -m "commit message"	//提交本地仓库
>
> [git reset HEAD^ + git reset HEAD]	//撤销commit，再撤销add。再回到第三步去撤销。
>
> git log	//查看commit日志
>
> git remote add origin `ssh`	//连接远程仓库
>
> git push -u origin master	//将本地内容推送到远程仓库



## 7. HEAD是什么？Origin是什么？master是什么？Branch是什么？git push -u中的u是什么？

https://www.zhihu.com/question/20019419