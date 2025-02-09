#### crontab 进行 定时任务的设置   
- **概述**
	1. **任务调度:**
		是指系统在某个时间执行的特定的命令或程序
	2. **任务调度分类:**
		( 1 ) 系统工作:    有些重要的工作必须周而复始地执行，如病毒扫描等
		( 2 ) 个别用户工作:    个别用户可能希望执行某些程序，如对 mysql 数据库的备份

- **基本语法**    `cron table`
	**crontab \[ 选项 ]**   

- **常用选项**
	1. **-e**    `edit`            编辑 **crontab** 定时任务
	2. **-l**    `list`             查询 **crontab** 任务
	3. **-r**    `remove`         删除当前用户所有的 **crontab** 任务

---

- **快速入门**
	1. 设置任务调度文件:    **/etc/crontab**
	2. 设置个人任务调度，执行 **crontab -e** 命令
	3. 接着输入任务到调度文件
	如:        **\*/1 \* \* \* \* ls -l /etc/ > /tmp/to.txt**
	意思是:    每小时的每分钟执行 **ls -l /etc/ > /tmp/to.txt** 命令

- **参数细节说明:**    ( 上述 5 个占位符的说明 )  
	1. 一小时当中的第几分钟    ( 范围:  0 - 59 )
	2. 一天当中的第几小时        ( 范围:  0 - 23 )
	3. 一个月当中的第几天        ( 范围:  1 - 31 )
	4. 一年当中的第几月            ( 范围:  1 - 12 )
	5. 一周当中的星期几            ( 范围:  0 - 7 )  ( 0 和 7 都代表星期日 )
 ps:    **\*** 表示每...都执行，**\*/1** 等价于 **\***

- **特殊符号说明:**
	1. **\***      代表任何时间，比如 第一个 **\*** 就代表一小时中每分钟都执行一次的意思
	2. **\`**      代表不连续的时间，比如 "**0 8,12,16 \* \* \*** 命令"，就代表在每天的 8 点 0 分，12 点 0 分，16 点 0 分都执行一次命令
	3. **\-**      代表连续的时间范围，比如 "**0 5 \* \* 1-6** 命令"，代表在周一到周六的5 点 0 分执行命令
	4. **\*/n**    代表每隔多久执行一次，比如 "**\*/10 \* \* \* \*** 命令"，代表每隔 10 分钟就执行一遍命令

- **案例:**
	![[crontab 案例说明.png]]

---

- **cron 相关指令**
	1. **crontab -r**                     终止任务调度
	2. **crontab -l**                     列出当前有哪些任务调度
	3. **service crond restart**    重启任务调度

