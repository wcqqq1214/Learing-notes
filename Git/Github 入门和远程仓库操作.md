	在终端中输入 code . 表示用 VSCode 打开该仓库

##### Github 入门

- 配置 **SSH Key**
	1. 在 `.ssh` 文件中， 使用 `ssh-keygen -t rsa -b 4096` 生成 SSH 密钥
	2. 回车后，输入生成密钥的文件名称
		1. 如果是第一次使用该命令，继续回车即可，会在 `.ssh` 下生成 `id_rsa` 的密钥文件
		2. 如果之前生成过密钥文件，直接回车的话会覆盖掉之前的密钥文件，而且操作不可逆
	3. 然后输入密码 ( 直接回车表示不需要密码 )
ps : 
1. 如果不是第一次生成 SSH 密钥，需要创建一个 `config` 文件，并添加下面的内容到文件中 ( 表示当访问 `github.com` 的时候，指定使用 SSH 下的 `test` 密钥 )
```
# github
Host github.com
HostName github.com
PreferredAuthentications publickey
IdentityFile ~/.ssh/test
```

2. 私钥文件 :    `id_rsa`        ( 不要向任何人透露 )
3. 公钥文件 :    `id_rsa.pub` ( 打开后，复制里面的内容可配置到 Github 上 )


- 克隆仓库
	`git clone <remote-address>`


- 推送更新内容到远程仓库
	`git push <remote-name> <branch>`


- 拉取远程仓库内容到本地仓库
	`git pull <remote-name>`


---
##### 远程仓库

- **添加远程仓库**
- `git remote add <remote-name> <remote-url>`
	为本地 Git 仓库添加一个远程仓库的引用 
	1. 把本地仓库链接到远程仓库
	2. `<remote-name>` 为远程仓库别名


- **推送代码到远程仓库**
- `git push -u <remote-name> <branch1>:<branch2>`
	把本地仓库的 `branch1` 推送给远程仓库的 `branch2`
	1. 如果远程分支 `branch2` 不存在，Git 会创建它
	2. 添加 `-u` 参数后，本地分支 `branch1` 会绑定到远程分支 `branch2`，下次只需运行 `git push` 和 `git pull` ( 不带 `-u` 表示不绑定 )
	3. 如果本地分支和远程分支名称相同，可只写一个名称


- **查看远程仓库**
- `git remote -v`
	查看当前仓库所对应的远程仓库的别名和地址


- **拉取远程仓库的内容**
- `git pull <remote-name> <remote-branch-name>:<local-branch-name>`
	把远程仓库的指定分支拉取到本地再进行合并
	1. 仓库名称和分支名称均可省略，默认拉取远程仓库名 `origin` 的 `master` 或者 `main` 分支
	2. 如果本地分支和远程分支名称相同，可只写一个名称
	3. 可以把 `pull` 换成 `fetch`，`fetch` 命令只是获取远程仓库的修改，但是并不会自动合并到本地仓库中，需要手动合并


- `git remote rm <remote-name>`
	删除远程仓库


- `git remote rename <old-name> <new-name>`
	重命名远程仓库




