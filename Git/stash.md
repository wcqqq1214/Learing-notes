

- `git stash push -m <message>`
	`stash` 操作可以把当前工作现场 "储藏" 起来， 等以后恢复现场后继续工作
	
	- 参数 :
	1. `-u` 参数表示把 所有未跟踪的文件 也一并存储
	2. `-a` 参数表示把 所有未跟踪的文件和忽略的文件 也一并存储
	3. `-m <message>` 参数表示存储的信息 ( 可以不写 )
		如果不指定 `-m`，Git 会使用默认格式生成消息 :
		`stash@{N}: WIP on branch-name: <commit-message>`
		1. `WIP` 表示 `Work In Progress`
		2. `branch-name` 是当前分支的名称
		3.  `<commit-message>` 是当前分支最后一次提交的消息


- `git stash list`
	查看所有的 `stash` 条目


- `git stash apply`
	应用最近的 `stash` ( 或指定的 `stash@{N}` )
	ps :    `apply` 不会删除 `stash` 记录，需要手动执行 `git stash drop` 来清理


- `git stash drop stash@{N}`
	删除指定的 `stash` 条目


- `git stash pop`    ( `apply` + `drop` )
	应用最近的 `stash`，并自动删除记录
	( 相当于先执行 `git stash apply` 然后执行 `git stash drop` )


- `git stash pop stash@{2}`
	恢复指定的 `stash`
	( `stash@{2}` 表示第 3 个 `stash`， `stash@{0}` 表示最近的 `stash`


- `git stash clear`
	清空所有的 `stash` 条目 ( 不可逆 )

ps :
1. `stash@{0}` 是最新的 `stash`
2.  删除某个 `stash` 后，编号会自动重新排列


---

- `git stash branch <new-branch-name> stash@{N}`
	 从 `stash` 创建新的分支
    1. 创建分支 `<new-branch-name>`，应用 `stash@{N}` 的改动
    2. `stash@{N}` 会自动删除

