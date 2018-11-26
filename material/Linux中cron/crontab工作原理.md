## 基本概念

**后台进程**

在后台运行的无须与用户交互的程序。在多任务操作系统上运行多个后台进程，有些后台进程（例如守护程序）从来都不需要用户输入，其它一些进程只是在用户忙于目前运行于前台的程序时才临时处于后台。

**cron的工作原理**

守护进程是一种后台进程。crond进程是众多守护进程中的一种，看看cron这个单词在字典里面的解释：
cron = chronograph,【unix】(时钟)守护程序，(精密)计时程序。简单的说，cron在预定的时间执行预订的命令或者脚本。

- cron由crond守护进程和一组表（crontab文件）组成。
- crond守护进程是在系统启动时由init进程启动的，受init进程的监视，如果它不存在了，会被init进程重新启动。这个守护进程每钟唤醒一次，并通过检查crontab文件判断需要做什么。
- 每个用户有一个以用户名命名的crontab文件，存放在/var/spool/cron/crontabs目录里。若管理员允许或者禁止其他用户拥crontab文件，则应编辑/etc/下面的cron.deny和cron.allow这两个文件来禁止或允许用户拥有自己的crontab文件。每一个用都可以有自己的crontab文件，但在一个较大的系统中，系统管理员一般会禁止这些文件，而只在整个系统保留一个这样的文件。

**crontab命令**

- **更新crontab文件**: crontab filename

  用来更新当前用户的crontab文件。**注意**：只是将filename指定的文件中的crontab条目写入用户的crontab文件（以用户名命名）中并清空crontab文件原来的条目，并不是将filename指定的文件复制/var/spool/cron/crontabs目录。

- **列出crontab文件**: crontab -l

  列出当前用户的crontab文件的crontab条目

- **编辑crontab文件**: crontab -e


  编辑当前用户的crontab文件

- **删除crontab文件**: crontab -d

  删除当前用户的crontab文件

[转自: 详解Linux中cron/crontab工作原理](https://blog.csdn.net/wzyzzu/article/details/42167911)