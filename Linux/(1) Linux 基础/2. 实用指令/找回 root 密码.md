**( CentOS7后 )**

***ps:    可能会作为面试题 ! ! !***

1. 启动系统，进入开机界面，在界面中按 **e** 进入编辑界面

2. 进入编辑界面，移动光标，找到以 **Linux16** 开头内容的行，在行的最后面输入 **init=/bin/sh**

3. 输入 **Ctrl + x** 进入 **单用户模式**

4. 输入 **mount -o remount,rw /** ，按下 **回车**   ( 注意空格 ! ! ! ) 

5. 输入 **passwd** ，按下 **回车** ，输入密码，再次确认密码，出现 **passwd......** 说明密码修改成功

6. 输入 **touch /.autorelabel**，按下 **回车**   ( **touch** 与 **/** 后面有一个空格 )

7. 输入 **exec /sbin/init** ，按下 **回车** ，等待系统自动修改密码，完成后，系统会自动重启，新的密码生效   ( **exec** 与 **/** 后面有一个空格 )