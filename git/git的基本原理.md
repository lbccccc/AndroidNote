   #### 基本概念

1. git仓库由一个个commit组成，某些commit上会有一些branch指向他们。

2. branch的本质是引用。head是一个特殊的引用，始终指向当前位置，位置包括commit和branch。

3. add指令，把指定内容放进暂存区。

4. branch与什么时候创建的无关，也和仓库的起点无关，只和它当前指向哪个commit有关。

5. master是git默认的branch（可以修改），在clone的时候，把仓库取到本地时默认是master。

6. origin 开头的branch 是远端仓库的本地镜像，作用是方便本地查看远端仓库的branch状态，只会在两种情况下更新，1 push成功后会把它所对应的origin branch更新到当前的commit 2 执行pull 或者fetch ，从远端拿到了所有的最新branch状态，所以会更新所有的origin branch

7. HEAD也是引用，但不是branch，他代表现在所处的位置，HEAD不仅可以指向某个commit，也可以指向某个branch比如master，feature1


#### 基本原理

1. 每次commit的时候，HEAD会随着新的commit一起移动，如果HEAD还指向了某个branch，他也会带着branch一起移动

2. clone的时候做的两件事，首先把整个仓库的所有branch都取下来，并且把从初始commit到达这些branch的路径上所有的commit都取下来，然后从初始commit开始向master所指向的commit一个个的把每个commit应用一遍，最后会得到一个“当前”状态的仓库内容，写进Git所在的目录（叫做working tree）

3. git log 从HEAD指向的commit开始，倒序的显示每一个commit的摘要信息

4. 在执行push命令把本地内容推到远端仓库，远端仓库的HEAD会跟随默认branch，而不是本地HEAD。也就是只有push master分支到远端的时候，远端的HEAD才会移动，如果push的不是master，那么远端仓库显示不会变，但是内容还是会传过去。

5. push的原理 push 把当前branch推送到远端仓库的指定分支，做两件事 1 把HEAD所指向的branch 推送到远端仓库 2 从这个branch向前回溯，远端仓库缺少的每一个commit也推送到远端仓库。 最后会把push 的branch的本地镜像origin xxxx也更新

6. pull的原理 pull和push是相反的，先把所有远端的branch最新的位置更新到本地的origin镜像 ，然后要到达这些branch，本地所缺少的所有commit也取到本地 最后把origin 镜像的内容合并到当前的branch

7. merge的原理  把当前commit和指定commit（可以用哈希值指定，也可以用直接或者间接指向它的引用指定，比如master或者HEAD）进行合并，然后创建一个新的commit
