## Git
**https://learngitbranching.js.org/?demo=&locale=zh_CN**   

### 改变Head或移动分支
`git checkout HEAD^`找到当前分支前一个 `git checkout HEAD~n`找到当前分支前n个 `git checkout C4`切换到指定结点   
^和~可以组合使用 比如`git checkout HEAD~^2~2` 切换到父节点的第二个父节点的爷爷节点

### 强制移动分支(回滚)
`git branch -f main HEAD~3`     
上面的命令会将 main 分支强制指向 HEAD 的第 3 级父提交

### git reset
git reset 通过把分支记录回退几个提交记录来实现撤销改动。你可以将这想象成“改写历史”。git reset 向上移动分支，原来指向的提交记录就跟从来没有提交过一样。

### git revert
虽然在你的本地分支中使用 git reset 很方便，但是这种“改写历史”的方法对大家一起使用的远程分支是无效的哦！    
为了撤销更改并分享给别人，新提交记录 C2' 引入了更改 —— 这些更改刚好是用来撤销 C2 这个提交的。也就是说 C2' 的状态与 C1 是相同的。    
revert 之后就可以把你的更改推送到远程仓库与别人分享啦。    

### git cherry-pick C1 C2 C3
只抓取另外一个分支的某几个提交合并到当前分支

### git rebase HEAD~n -i
打开ui界面选择回撤的提交数和顺序

### git commit --amend
本质上就是生成了新的commit，替代了上一次commit的位置

### git tag
打标签`git $tagname $commitnode`

### git describe
表述当前结点最近的tag信息

### rebase和merge的区别
merge: 通过创建一个新的commit合并两个分支   
rebase: 直接将分支整合到原分支的头部 形成一条commit线   
**不要对公共分支进行rebase**   
**https://www.cnblogs.com/FraserYu/p/11192840.html**