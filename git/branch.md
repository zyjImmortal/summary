* 查看当前所属分支
`git branch`
* 切换分支
`git checkout -b dev,-b表示如果不存在帮我创建分支`
`git push origin dev:dev`推到dev分支，如果没有就创建一个分支

* 在本地有修改，但是代码仓库也有修改内容时，git操作方法
* git stash save 文件 ,将文件放入暂存区，然后git pull最新代码，git stash pop stash@{0}将暂存内容合并到本地并从暂存区删掉，也可以执行git stash apply stash@{0}但是这个命令只会合并到本地但是不会删除暂存，0这里代表暂存id，可以通过git stash list查看当前所有暂存区的内容,合并以后执行git add 文件，将修改放入暂存区，然后git commit -m ""，在git push提交