- 介绍        `RedHat Package Manager`
	1. rpm 是互联网下载包的打包及安装工具，它包含在某些 Linux 分发版中，生成具有 **.RPM** 扩展名的文件，类似 Windows 的 **setup.exe**
		( 这一文件格式名称虽然打上了 RedHat 的标志，但理念是通用的 )
	2. Linux 的分发版本都有采用 ( SUSE, RedHat, CentOS 等 )，可以算是公认的行业标准了


- **简单查询指令**
	**rpm -qa | grep xx**        查询已安装的 rpm 包列表


- **包名基本格式**
	例:    **firefox-60.2.2-1.el7.centos.x86_64**
	1. `firefox`         名称
	2. `60.2.2-1`       版本号
	3. `el7.centos.x86_64`    表示 centos7.x 的 64 位系统
		( 如果是 `i686` `i386` 表示 32 位系统，`noarch` 表示通用 )


- **rpm 包的其他查询指令**
	1. **rpm -qa**                    查询所安装的所有 rpm 软件包
		1. rpm -qa | more
		2. rpm -qa | grep xx
	
	2. **rpm -q 软件包名**        查询软件包是否安装
	
	3. **rpm -qi 软件包名**       查询软件包信息    `information`  
	
	4. **rpm -ql 软件包名**       查询软件包中的文件    `list` 
	
	5. **rpm -qf 文件全路径名**    查询文件所属的软件包    `file`
		例:
		1. rpm -qf /etc/passwd
		2. rpm -qf /root/install.log


---

- **卸载 rpm 包**
	- 基本语法:    `erase`
		**rpm -e 软件包名**
	
	- 细节说明:
		1. 如果其他软件包依赖于要卸载的软件包，卸载时会产生错误信息
		2. 可以通过增加参数 **--nodeps** 强制删除，但不推荐这样做，因为依赖于该软件包的程序可能无法运行    `rpm -e --nodeps xxx`
			*ps:    `no dependencies`，不检查依赖关系*

- **安装 rpm 包**
	- 基本语法:    `install`
		**rpm -ivh RPM包全路径名称**
		1. i     `install`   安装
		2. v    `verbose`   提示
		3. h    `hash`        进度条



