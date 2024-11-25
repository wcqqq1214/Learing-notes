
*ps:    也可以把某个日志文件的轮替规则，写到 /etc/logrotate.d/ 目录*
```
# see "man logrotate" for details
# rotate log files weekly 
# 每周对日志文件进行一次轮替
weekly

# keep 4 weeks worth of backlogs 
# 共保存4份日志文件，当建立新的日志文件时，旧的会被删除
rotate 4

# create new (empty) log files after rotating old ones
# 创建新的空的日志文件，在日志轮替后
create

# use date as a suffix of the rotated file
# 使用日期作为日志轮替文件的后缀
dateext

# uncomment this if you want your log files compressed
# 日志文件是否压缩，如果取消注释，则日志会在转储的同时进行压缩
#compress

# RPM packages drop log rotation information into this directory
# 包含 /etc/logrotate.d 目录中所有的子配置文件，也就是说会把这个目录所有的子配置文件读取进来
include /etc/logrotate.d

# 下面是单独设置，优先级更高

# no packages own wtmp and btmp -- we'll rotate them here
/var/log/wtmp {
    monthly # 每月对日志文件进行一次轮替
    
    create 0664 root utmp # 建立的新日志文件，权限是 0664，所有者是 root，所属组是 utmp 组
    
	minsize 1M # 日志文件最小轮替大小是 1MB，也就是日志一定要超过 1MB 才会轮替，否则就算时间达到一个月，也不进行日志转储
	
    rotate 1 # 仅保留一个日志备份，也就是只有 wtmp 和 wtmp.1 日志保留
}

/var/log/btmp {
    missingok # 如果日志不存在，则忽略该日志的警告信息
    monthly
    create 0600 root utmp
    rotate 1
}

# system-specific logs may be also be configured here.
```

####  参数说明
  
- `daily`                          日志的轮替周期是每天
- `weekly`                        日志的轮替周期是每周
- `monthly`                      日志的轮替周期是每月
- `rotate 数字`                保留的日志文件的个数，0 指没有备份
- `compress`                     日志轮替时，旧的日志进行压缩
- `crea te mode owner group`     建立新日志，同时指定新日志的权限、所有者和所属组  
- `mail address`               当日志轮替时，输出内容通过邮件发送到指定的邮件地址
- `missingok`                    如果日志不存在，则忽略该日志的警告信息
- `notifempty`                  如果日志为空文件，则不进行日志轮替
- `minsize 大小`               日志轮替的最小值，也就是日志一定要达到这个最小值才会轮替，否则就算时间 达到也不轮替
- `size  大小`                   日志只有大于指定大小才进行日志轮替，而不是按照时间轮替  
- `copytruncate`               复制内容到备份文件后截断原日志
- `dateext`                        使用日期作为日志轮替文件的后缀
- `sharedscripts`              在此关键字之后的脚本只执行一次
- `prerotate/endscript`   在日志轮替之前执行脚本命令
- `postrotate/endscript` 在日志轮替之后执行脚本命令