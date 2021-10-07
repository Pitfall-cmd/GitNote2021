

## 03 | 使用Git之前需要做的最小配置

添加最小配置:
git config --local user.name 'lunzi'
git config --local user.email 'lunzi@163.com'


参数区别:
git config --local ##只对某个仓库有效,切换到另外一个仓库失效
git config --global ##当前用户的所有仓库有效,工作当中最常用
git config --sysstem ##系统的所有用户,几乎不用

查看配置:

git config --list --local ##只能在仓库里面起作用, 普通路径git不管理
git config --list --global
git config --list --system

local的在.git/config里面；global的在个人home目录下的.gitconfig里面；system应该在git安装目录的下



## 05 | 通过几次commit来认识工作区和暂存区

![image-20210930211329466](D:\ADeskTop\大四上学期\Git\Git.assets\image-20210930211329466.png)

git add .：将工作空间新增和被修改的文件添加的暂存区
git add -u:将工作空间被修改和被删除的文件添加到暂存区(不包含没有纳入Git管理的新增文件)

git add -u：将文件的修改、文件的删除，添加到暂存区。
git add .：将文件的修改，文件的新建，添加到暂存区。
git add -A：将文件的修改，文件的删除，文件的新建，添加到暂存区。
工作中一般是用到 git add . 或者 git add -A

git add -A相对于git add -u命令的优点 ： 可以提交所有被删除、被替换、被修改和新增的文件到数据暂存区，而git add -u 只能操作跟踪过的文件
git add -A 等同于git add -all

==git commit -m"message" 或者是单引号   用于把暂存区的内容提交==

==git status 查看状态==



## 06 | 给文件重命名的简便方法



==git reset --hard 清除暂存区==



mv readme readme.md 删除文件readme 创建新文件readme.md
git add readme.md 添加到暂存区
git rm readme 将原来的文件删除掉
这三步可以直接变成一步 ==git mv readme readme.md 将readme重新命名成readme.md==

修改日期  git log --date=format:%Y-%m-%d\ %H:%M:%S（MAC下）         git log --date=iso          git log --date=short





## 07 | 通过git log查看版本演变历史

本节的一些演示命令总结
• git log --all 查看所有分支的历史
• git log --all --graph 查看图形化的 log 地址
• git log --oneline 查看单行的简洁历史。
• git log --oneline -n4 查看最近的四条简洁历史。
• git log --oneline --all -n4 --graph 查看所有分支最近 4 条单行的图形化历史。
• git help --web log 跳转到git log 的帮助文档网页



## 08 | gitk：通过图形界面工具来查看版本历史

gitk 后面可以跟上文件的路径， 这样能看单个文件的修改历史的具体内容。非常有用。



## 09 | 探密.git目录

// 09|探秘.git目录
//cat命令主要用来查看文件内容，创建文件，文件合并，追加文件内容等功能。
cat HEAD 查看HEAD文件的内容
git cat-file 命令 显示版本库对象的内容、类型及大小信息。

> 经过测试 git cat-file 首先后面的必须是git对象，它可以是对象名字或者对象哈希值  ，如果是123.docx 这种不是git对象就不行

git cat-file -t b44dd71d62a5a8ed3 显示版本库对象的类型
git cat-file -s b44dd71d62a5a8ed3 显示版本库对象的大小
git cat-file -p b44dd71d62a5a8ed3 显示版本库对象的内容

HEAD：指向当前的工作路径
config：存放本地仓库（local）相关的配置信息。
refs/heads:存放分支
refs/tags:存放tag，又叫里程牌 （当这次commit是具有里程碑意义的 比如项目1.0的时候 就可以打tag）
objects：存放对象 .git/objects/ 文件夹中的子文件夹都是以哈希值的前两位字符命名 每个object由40位字符组成，前两位字符用来当文件夹，后38位做文件。



.git 文件目录

• COMMIT_EDITMSG
• config 当前 git 的配置文件
• description （仓库的描述信息文件）
• HEAD （指向当前所在的分支），例如当前在 develop 分支，实际指向地址是 refs/heads/develop
• hooks [文件夹]
• index
• info [文件夹]
• logs [文件夹]
• objects [文件夹] （存放所有的 git 对象，对象哈希值前 2 位作为文件夹名称，后 38 位作为对象文件名, 可通过 git cat-file -p 命令，拼接文件夹名称+文件名查看）
• ORIG_HEAD
• refs [文件夹]
   • heads （存放当前项目的所有分支）
   • tags (存放的当前项目的所有标签，又叫做里程碑)

• cat 命令， 功能：用来显示文件。 例如 cat text.md 显示 text.md 文件的内容
• ls -al 命令， 表示列出当前目录下的所有文件（包括隐藏文件）
• git cat-file -t 命令 ， 查看 git 对象的类型
• git cat-file -p 命令， 查看 git 对象的内容
• git cat-file -s 命令， 查看 git 对象的大小



==文件内容相同，git眼里就是唯一的blob ， 这很常见，就是享元模式吧==。 lua，Python等语言中，相同字符串，其实只分配一次内存，地址一样，指向同一个对象，可以节省内存。



## 10 | commit、tree和blob三个对象之间的关系

git有3种对象：commit、tree、blob。每次的提交，都是一个commit，一个commit又是包含了一棵tree，每个tree里面又是包含了多棵tree和blob，而文件的的最终形式是blob。对于blob，git会认为文件内容相同，就使用同一个blob，这样就极大的避免了频繁的提交时，git的存储空间的急剧上升。

> 我理解就是 tree和 commit 都是个文件夹，blob是文件，没有修改过的blob指向同一个blob，这样避免git存储空间急剧上升



## 12 | 分离头指针情况下的注意事项

基于某个commit做修改 git checkout commitid
==查看分支 git branch -av==
创建分支 git branch 分支名 commitid
==图形化查看分支情况 gitk --all==
这时会出现分离头指针的情况，此时你所做的修改没有对应任何分支，当切换分支时可能会出现丢失所修改的代码的情况。
分离头指针的应用场景：想测试某个应用场景或者方案，可以使用分离头指针，如果发现方案不合适，可以直接reset其他分支即可，这样就省去了创建删除分支的步骤

## 13 | 进一步理解HEAD和branch

==以某个commit为节点创建分支并切换   git checkout -b 分支名 commitID==

==切换到某一个分支 git checkout 分支名==

git diff commitid1 commitid2 可以比较 两个commit之间的不同



## 14 | 怎么删除不需要的分支？

git branch -d branch名字

## 15 | 怎么修改最新commit的message？

git commit --amend   对当前分支最新的commit修改

## 16 | 怎么修改老旧commit的message？

==git rebase -i 修改节点的父节点ID   选择==**reword**  在修改message  ；rebase的概念是基于什么修改，所以是基于父节点来修改的意思

 如果本身修改的节点是根节点  git rebase -i --root



 message 也是commit的属性，是确定commit的hash值的一个因子。其次，second_commit 有个 parent 的属性，parent的内容发生了变化，因此，second_commit 的hash也变了。

以此类推，后面的commit也发生了变化。

## 17 | 怎样把连续的多个commit整理成1个？

==git rebase -i 修改节点的父节点ID   选择== squash 



## 18 | 怎样把间隔的几个commit整理成1个？

见视频

## 19 | 怎么比较暂存区和HEAD所含文件的差异？

> HEAD 就是当前分支的最新Commit ，暂存区和HEAD进行比较

vi index.html 修改index.html的内容
git add index.html 将修改的文件添加到暂存区
git status 显示在哪个暂存区 有没有文件改变将要提交
==git diff --cached 查看暂存区和最新COMMIT（HEAD）的比较==
git commit -m'Add the frist command with config' 做提交操作

## 20 | 怎么比较工作区和暂存区所含文件的差异？

git  diff 比较的是工作区和暂存区的差异  如果要比较具体的文件名 则 ==git diff -- 文件名== 同理应用其他

git diff HEAD 比较的是 HEAD和工作区的差异



## 21 | 如何让暂存区恢复成和HEAD的一样？

git reset HEAD

git reset 有三个参数
--soft 这个只是把 HEAD 指向的 commit 恢复到你指定的 commit，暂存区 工作区不变
--hard 这个是 把 HEAD， 暂存区， 工作区 都修改为 你指定的 commit 的时候的文件状态
--mixed 这个是不加时候的默认参数，把 HEAD，暂存区 修改为 你指定的 commit 的时候的文件状态，工作区保持不变

很少会有需要将暂存区的内容恢复成和HEAD一样的。因为==如果感觉暂存区的修改没有工作区的内容好==，可以直接将工作区的内容添加到暂存区，那么暂存区会以新添加的文件状态为准

## 22 | 如何让工作区的文件恢复为和暂存区一样？

git checkout  -- 文件名 让工作区恢复为暂存区的内容

也可以 git restore 文件名 



## 23 | 怎样取消暂存区部分文件的更改？

跟21 一样  加上文件名  git reset HEAD -- 文件名 

或者  git restore --staged 文件名

回复暂存区的修改 到 commit HEAD

## 24 | 消除最近的几次提交

修改了工作区，恢复：git checkout

add后，想撤销： git reset HEAD

commit后，想撤销： ==git reset--hard hash值==

## 25 | 看看不同提交的指定文件的差异

==git diff commit-id1 commit-id2 path-to-filename==

## 26 | 正确删除文件的方法

git rm 文件名

直接把删除文件的情况 放到 暂存区



## 27 | 开发中临时加塞了紧急任务怎么处理？

==git stash==  把当前的工作区的任务 加到 一个stack 中  ，此时 git status 会显示一切正常

==git stash list==  查看 stack中的任务

把工作区的任务压倒stash 中 然后去做紧急任务，完成紧急任务之后。

使用 ==git stash apply== 或者 ==git stash pop== 来把stack中的任务拿出来，其中 apply是拿出来 但不删除 list中的信息，而pop是拿出来 并且list中的信息 也删除

>  假如我们想拿list中的第几个任务

git stash list 命令显示的最左一列就是stash的序号，如stash@{2} 和 stash@{1}，序号中数字大的代表的是较早的stash。我们pop的时候可以加具体的序号，不加序号的（缺省情况下）为 stash@{0}。

用法：git stash pop stash@{2}
      git stash pop = git stash pop stash@{0}
      

## 28 | 如何指定不需要Git管理的文件？

一些可以通过构建生成的文件不需要管理，比如java源代码编译之后生成.class文件 这个class文件完全可以通过java源文件编译生成 ，所以不需要纳入git管理之中

首先要写一个 ==.gitignore 文件== ，在文件中==写入你不管理的文件==

*.class 不管 所有class文件  file-name/ ，不管理所有file-name 文件下的内容

## 29 | 如何将Git仓库备份到本地？

四种备份协议

> 哑协议

D:\GitLearning\666-backup>==git clone== ==--bare== ==D:\GitLearning\git_learning\.git==  ==ya.git==
Cloning into bare repository 'ya.git'...
done.

--bare指的是clone 裸仓库 D盘的文件 .git 克隆到目标  ya.git

> 智能协议  加了file://   能显示进度 

D:\GitLearning\666-backup>git clone --bare file://D:\GitLearning\git_learning\.git  ==zhineng.git==
Cloning into bare repository 'zhineng.git'...
remote: Enumerating objects: 18, done.
remote: Counting objects: 100% (18/18), done.
remote: Compressing objects: 100% (14/14), done.
remote: Total 18 (delta 4), reused 0 (delta 0), pack-reused 0
Receiving objects: 100% (18/18), 4.72 KiB | 2.36 MiB/s, done.
Resolving deltas: 100% (4/4), done.

> 建立与远端备份的连接  与更新

查看当前已经建立连接的信息  ==git remote -v==

删除不需要的连接信息  git remote remove <name>

==与上面建立好的远程仓库连接==

git remote add remoteRepoName file:///XXXX/remoteRepoName.git

本地发送一些改动，比如新增分支develop

推送分支变动到远程

git push --set-upstream remoteRepoName develop  （好像直接git push remoteRepoName也行）

再到远程库下面查看就会发现远程也有develop分支了

例子：

D:\GitLearning\git_learning>==git remote add **zhineng2** file://D:\GitLearning\666-backup\zhineng.git==

D:\GitLearning\git_learning>git push zhineng2
Everything up-to-date





## 33 | 把本地仓库同步到GitHub

==git fetch NAME== 这个只是把远端的取下来 但是不合并 ，你看gitk的时候 会发现它是两颗独立的树

> git fetch 拉取远程版本库到本地, 请问, 是将远程的代码拉取到工作区吗, 如果我之前工作区有新内容的话,然后 git fetch拉去远程代码到本地, 会不会发生覆盖行为呢
>
> 作者回复: fetch不会拉到工作区的，只是把**本地的远端分支和服务端对应的远端分支保持一致。**



于是我们需要合并

首先切换到自己合并想要的分支

==git merge github/main== 发现会报错，说不能合并不相关的两棵树,因为它们是Non-fast forward （非直推式，关系

> 作者回复: 举个例子，本地分支往远端分支做push，如果远端分支不是本地分支的祖先，那它俩就不是 fast forward 了。反之，它俩就是fast forward的关系。

于是我们可以用  gi==t merge -h== 来查看帮助 

发现可以采用如下参数  ==git merge  --allow-unrelated-histories github/main==



## 34 | 不同人修改了不同文件如何处理？

首先在远端创建一个==新的分支branch2==

git clone git@github.com:Pitfall-cmd/git_learning.git  git_learning2 克隆远端的到git_learning2文件

checkout -b branch2 origin/branch2 ==基于远程分支 创建自己的本地分支，因为不能直接修改 需要创建之后在修改==

修改完成后

git add -u 把所有更新的操作添加到暂存区 

git commit -m"message" 提交commit

git push  这里可以直接push 不需要指名push到哪个远端 ，因为我们是直接clone下来的

然后远端就更新了

然后再git_learning那里  git branh-av 发现没有把远端的branh2跟新到本地的记录上，==所以需要使用fetch==

git fetch github

git checkout -b  branch2  github/branch2 然后也要创建本地分支

然后修改

git add -u

git commit -m"message2"

但是呢，这时候git_learning2又做了某些修改 并提交，这时会报错。需要merge，因为文件已经修改了，fetch的不是最新的。于是我们需要先fetch把远程的拉到本地，在合并，然后push就没问题了

* branch2                  48bc6d1 **==[ahead 1, behind 1]==** 34test2

git merge github/branch2   不过由于我这里搞错了，我修改的是同一个文件 所以出了一点小小的问题，不过如果是修改不同的文件 就可以了。

## 35 | 不同人修改了同文件的不同区域如何处理？

可以merge一下 ，或者pull  反正 pull就完事了。

## 36 | 不同人修改了同文件的同一区域如何处理？

首先两个人都已经 git pull 过，其中A率先提交了，这时候B提交的时候 就会报错。 

于是我们可以git pull  ，由于git pull 自动包含 git fetch 和 git merge ，由于在同一区域**所以就会Conflict 冲突**，于是我们==要修改冲突的文件，最后 add ，commit== 一下 然后 push就可以提交了。

## 37 | 同时变更了文件名和文件内容如何处理？

A改变了文件名，然后提交了push，B对原文件修改了其中的内容

那么B提交的时候会出错，这时候我们只需要pull重新拉一下，他会自动合并。这也是git的强大之处，会看前面的内容，我们会发现其实都是pull，只要pull就完事了。出现conflict解决conflict 就行了。

## 38 | 把同一文件改成了不同的文件名如何处理？

假如A修改了名字 A率先提交，这时候B修改了名字 B提交报错

于是我们git pull 一下，但这个时候 就会出现冲突，我们使用git status来显示问题，然后按照步骤选择删除rm或者增加 add

## 39 | 禁止向集成分支执行push -f操作

git reset --hard commitid  会让HEAD指向目标commit 同时之后的所有commit消失了

git push -f  强制更新远端，  **禁止用！！**  会把之前的commit都删除的！

**可以使用 git reflog 命令查找历史，然后利用 git reset --hard HAED@{n} 的方式恢复**

## 40 | 禁止向集成分支执行变更历史的操作

意思就是说 不要去变更已经集成好的分支，因为这些已经被其他同时clone过了，你rebase 去合并commit 会影响他们。但是你自己的分支，自己新弄得commit 可以适合合并rebase，只要是拉下来的 就不能去rebase！

>  请教，对于自己在本地的多次commit，我想把他合并成一次commit，还没有push的情况下，能否做rebase操作？

> 作者回复: 这种情况最适合用rebase了



## 41 | GitHub为什么会火？

让整个协作和编码更容易

## 42 | GitHub都有哪些核心功能？

code review 使用Market place 来做

project management

team management

wiki 相当于使用手册

code hosting  主要功能

## 43 | 怎么快速淘到感兴趣的开源项目?

光标放到搜索栏  回车 之后选择==advanced search==

单纯的搜索只会在 仓库名和描述里搜索，因此搜出来的东西很少

你也可以 在搜索之后加上这个 就可以了在readme里面搜索了   in:readme  starts>1000 等等



## 49 | 如何挑选合适的分支集成策略？

branch01-test1

master -test01

一些测试与思考：

现在github创建了一个分支branch01，然后本地fecth一下，然后git checkout -b xxx remotexxx 创建并切换。

修改branch01的内容，提交。然后切换到master的内容，修改。提交。

**合并的时候就在github端 提出一个pull request** 合并，由于我修改的是同一个区域，会产生conflict，在github端解决冲突，就可以merge成功了，这里merge的时候有三个选项，就是所谓的分支集成策略，有merge，squash还有rebase。具体可以看本节视频，这三种的意思。

**不一样的地方是我合并之后在github删除了branch0**1，这样删除之后本地的branch01还在，**这时候由于master更新了**，我想pull下来，就报错了

```bash
You asked to pull from the remote 'github', but did not specify
a branch. Because this is not the default configured remote
for your current branch, you must specify a branch on the command line.
```

由于本地的branch01还在，github却不在了，就会让你指定你要的branch。

指定的命令为 **==git pull github master==** ,意思就是去 github的远端 找master分支

更新完后，我又在本地添加了内容，这时候更新并push到远端，我以为会报错，因为本地branch01还在但是github端的branch01却不再了，**但是没想到他没报错**,

```bash
  branch01                bd52864 branch01 -test1
* master                  736b149 Merge pull request #1 from Pitfall-cmd/branch01
  remotes/github/branch01 bd52864 branch01 -test1
  remotes/github/master   736b149 Merge pull request #1 from Pitfall-cmd/branch01
```

这时候我突然想起来，当时删掉远端branch01的时候，重新fetch的时候，还是有远端的本地记录` remotes/github/branch01 bd52864 branch01 -test1` ,branch01本身还是没有改变，master改变只提交master部分，所以不会报错。
 测试：github 端修改文件，同时也恢复了branch01，看看会发生什么情况，写这个文字之前pull的时候发现 branch01被更新了，但是还是提示我需要specify a branch。所以对master修改一下 看看本地master是否也能被pull到

