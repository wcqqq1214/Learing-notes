
- 基本介绍
	**restore** 命令用来恢复已备份的文件，可以从 **dump** 生成的备份文件中恢复原文件

- 基本语法
	**restore \[ 模式选项 ] \[ 选项 ]**
	
	- **模式选项**    ( 在一次命令中只能指定一种模式，不能混用 )
		1. **-C**    对比模式，将备份的文件与已存在的文件相互对比
		
		2. **-i**     交互模式，在进行还原操作时，restore 指令将依序询问用户
		
		3. **-r**     还原模式    ( 最常用 )
		
		4. **-t**     查看模式，显示备份文件的目录结构
	
	- **选项**
		1. **-f <备份文件>**    从指定的文件中读取备份的数据，进行还原操作


---

- 应用案例 : 
	1. restore **-C 比较模式**，比较备份文件和原文件的区别
		1. mv /boot/**Hello.cpp** /boot/**Hello.java**
			restore -C -f /opt/boot.bak1.bz2
		2. mv /boot/**Hello.java** /boot/**Hello.cpp**
			restore -C -f /opt/boot.bak1.bz2
	
	2. restore **-t 查看模式**，看备份文件中有哪些数据/文件
		restore -t -f /opt/boot.bak0.bz2
	
	3. restore **-r 还原模式**
		( 如果有增量备份，需要把增量备份文件也进行恢复，有几个增量备份文件，就恢复几个，**按顺序来恢复** )
		1. mkdir /opt/boottmp
		2. cd /opt/boottmp
		3. restore -r -f /opt/boot.bak0.bz2    # 恢复到第1次完全备份状态
		4. restore -r -f /opt/boot.bak1.bz2    # 恢复到第2次增量备份状态

