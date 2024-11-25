
- **启动流程介绍**
	1. 首先 Linux 要通过自检，检查硬件设备有没有故障
	
	2. 如果有多块启动盘的话，需要在 **BIOS** 中选择启动磁盘
	
	3. 启动 **MBR** 中的 **bootloader** 引导程序
	
	4. 加载内核文件
	
	5. 执行所有进程的父进程，以及 **systemd**
	
	6. 欢迎界面

- 在 Linux 的启动流程中，加载内核文件时的关键文件 :
	1. **kernel** 文件 :        **vmlinuz-3.10.0-957.el7.x86_64**
	2. **initrd** 文件 :         **initramfs-3.10.0-957.el7.x86_64.img**
		`init ram file system`


