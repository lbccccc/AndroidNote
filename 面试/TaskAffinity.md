在第一次打开一个App的时候，这个task的taskAffinity就是创建的activity的taskAffinity，activity的taskAffinity默认和所在的application一致。

如果一个配置了singletask的activity入栈，那么就会检查新的activity和task的taskAffinity是不是一样。如果相同就继续入栈，如果不同，新的activity就会进入和他的taskAffinity相同的栈里或者创建一个新的task


如果这个activity来自别的app，那么就会发生task的切换，如果就来自于自己的app，那么就会直接入栈。也可以自己设置taskAffinity，所以就算是来自自己所在的app，那么也会切换task


在最近任务列表里会列出有的task，但是他们的taskAffinity需要不一样，当多个task具有相同的taskAffinity的时候，只会显示最新展示过的那个






当activity1启动一个启动模式为singletask的activity2时，如果activity2不是本app的，也就是说他的taskAffinity不和当前的task一样，activity2就会自己创建一个task，再把整个栈压在启动他的task的上面（仔细观察会发现，这种方式的入场动画是应用程序之间切换的入场动画（也就是task之间切换的动画），而不是普通的activity入场的动画）
