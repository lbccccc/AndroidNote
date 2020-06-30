
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

     
