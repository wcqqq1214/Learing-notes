
- `git rebase <branch-name>`
	`rebase` 将当前分支上的提 "移到" 目标分支 `branch-name` 的最新提交之上
	1. 但是， 如果多人协作时， 不要对已经推送到远程的分支执行 `rebase `操作
	2. 因为 `rebase` 会重写历史，改变提交的哈希值
	![[git rebase.png]]


- `git pull --rebase`
	拉取远程仓库的代码，并且将本地的更改应用到远程代码的最新提交之后
	- 和 `git pull` 的区别 :
		1. `git pull` 使用 `merge` 合并本地和远程的代码
		2. 而 `git pull --rebase` 使用 `rebase` 合并
	
	- 案例 :
		`git pull --rebase origin main`
		拉取远程仓库的更新，并尝试将远程的 `main` 分支与本地的 `main` 分支合并 ( 但是会将本地提交 "移到" 远程的提交之后，保持历史的线性结构 )


---

ps :

多人基于同一个远程分支开发的时候，如果想要顺利 `push` 又不自动生成 `merge commit`，建议在每次提交都按照如下顺序操作 :

```
git stash

git pull --rebase

git push

git stash pop
```

