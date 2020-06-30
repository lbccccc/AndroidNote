
## Feature Branching 和其他常用指令(1)

### 

#### Feature Branching 

用于：每开发一个新功能或者做一个修复的时候都要用单独的分支，做完之后再merge到master

- ​	本地merge：由于别人在你之前可能已经push过了，所以你的push可能会失败。所以一般都要先pull一下，再push
- 使用github：先创建pull request ，在别人审阅完成之后，通过按钮实现在线merge
  - [ ] pull request是开发者对远端仓库提出的“ 拉取某个branch ”的请求

#### merge冲突

当Git不知道怎么合并某两处冲突的修改时，会中断自动合并，并对冲突文件进行标记。解决方法：

1. 手动把文件修改好
2. 把修改的内容使用add加入暂存区
3. 使用 git merge --continue 来继续自动合并流程

#### rebase

把当前commit（包括它之前的commits）放应用到指定的需要rebase的commit上

[^注意]: Git中的任何一个commit都是永远不能改变的，所以rebase之后的每一个commit都是新产生的，而不是对原先的commit进行修改

#### rebase冲突

rebase的冲突解决方法和merge一样，把git merge --continue 改成 git rebase continue 就行

#### 提交过的东西想要修改

1. 最新的一条内容需要修改  git commit --amend （注意，所谓的修改只是方便理解，和上面说的一样，commit永远不会改变，所以实际上会产生一个新的commit）

2. 旧的内容需要修改 git rebase -i 【指定的commit的哈希值】 交互式rebase。 交互式rebase 不止可以修改，还可以增加和删除

3. 如果已经push到master了的内容需要删除：  

   不能用rebase ，因为master上面的东西是不能强行修改的，可以用git revert 【指定的commit哈希值】来撤销，原理是创建一个新的commit，内容是指定commit的所有相反内容

#### reset

   把当前branch指向指定的commit。

   - git reset 指定的commit

     移动到指定的commit，并把当前位置和指定位置的文件差异加入working tree

   - git reset --hard 指定commit

     移动到指定commit，并重置working tree

     
#### reset和checkout的区别

     它们都是移动HEAD，但是checkout移动的时候是自己移动，不带着branch，而reset会带着branch一起移动


## Feature Branching 和其他常用指令(2)

#### add -i 交互式 add

用法 ： git add -i  主要用于切割当前块，当一次性提交代码量太多的时候，切割多次上传

#### tag

另外一种引用类型

- 和branch的区别一：不能被改变
- 和branch的区别二：不能被HEAD指向
- 用处：设置持久标记，比如版本号
- tag和origin/master，origin/HEAD等等这些镜像有相似之处，都不能从本地改变位置，也不能被HEAD指向

#### reflog

用法：git reflog <branch>

用途 ：查看指定的引用(HEAD或者branch)的移动历史，从而找到某个之前的特定commit

#### cherry-pick

用法：git cherry-pick commit1 commit2

用途 ：应用指定的某个或者某些commit到HEAD处

#### github flow

- 有三种merge选项 
  1. 直接merge
  2. branch上所有的commit压合成一个后再commit
  3. branch所有的commit rebase到master之后再commit
- commit的时候会自动应用 --no-ff参数，强行增加一个merge commit 用于增加记录信息
- github的release和tag，tag是git的功能，release是github利用git的tag功能所提供的额外功能

#### git flow

两条持久的主线：master和develop

- master
  - 只用于存放所有的发布
  - 每次有了新的commit之后，立即打一个tag来记录
- develop
  - 用于存放不稳定版本的发布
  - develop不是直接用于开发feature的，开发feature需要专门的branch
  - develop在第一时间从master上分离出来
  - 需要开发任何功能的时候，从develop创建出新的fea branch，开发完成后再合并回develop（合并的时候用--no-f），然后删掉feature branc
  - 当下一个正式版本需要的所有功能都开发完成后，从develop上创建新的release branch，并在release branch合并到master后合并回develop(合并使用--no-f)，然后删掉release branch
- feature
  - 每次下一版本的功能开发完毕后，从develop.上创建
  - 创建完成后，更新版本号，然后单独做一个新的commit
  - 如果有bug fix， 直接在release branch上创建
  - bug fix完成后，合并到master和develop (使用--no-ff)，然后被删掉
- hotfix branches
  - 已经正式发布的产品发现bug，直接从master或者出问题的tag上创建hotfix branch ，进行紧急修复，修复完成后合并到master和develop（或者release branch 如果有的话）最后还是使用--no-f，然后删掉

#### 关于Git的分布式

两个本地仓库互相clone也是可行的，但是push的时候要记得把远端本地仓库的HEAD detach掉，或者设置远端仓库的 receive.denyCurrentBranch 为ignore或者warm


