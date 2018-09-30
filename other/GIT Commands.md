# GIT Commands



## branch

git  branch  -a：查看local以及remote分支



##checkout

+ git  checkout  branchName：切换到某个分支
+ git  checkout  -b  branchName：切换到某个分支，如果不存在则创建
  + 等价于git  branch  branchName， git  checkout  branchName
+ git  checkout  path/file：将某个文件切换到暂存区中的状态，暂存区为空则切换到最近的commit
+ git  checkout  commitId  path/file：将某个文件切换为指定commit时的状态。



## fetch 和 pull的区别

+ git fetch <远程主机名> <分支名>：从远端更新代码
+ git pull <远程主机名> <远程分支名>:<本地分支名>：从远端获取代码，并与本地分支合并



## push

git push <远程主机名> <本地分支名>:<远程分支名>

+ git push origin master： 将master推送到远端的master
+ git push origin  :master：将远端的master删掉（推送一个空branch过去）
+ git push origin：如果本端与远端存在追踪关系，那么可以直接推送
+ git push：如果当前分支只有一个追踪分支，那么可以进一步省略
+ git push -u origin master：将本地的master分支推送到origin的master，同时将其绑定（之后就直接push就行了）
+ git push --all origin：将本地的所有分支推送到origin
+ git push origin --tags：将tags一并推送到origin，默认不推送tags