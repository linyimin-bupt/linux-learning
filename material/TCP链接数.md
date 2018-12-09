## 一、查看哪些IP连接本机

```shell
netstat -an
```

## 二、查看TCP连接数

1) 统计80端口连接数

```shell
netstat -nat|grep -i "80"|wc -l
```

2）统计httpd协议连接数

```shell
ps -ef|grep httpd|wc -l
```

3）统计已连接上的，状态为"established"

```shell
netstat -na|grep ESTABLISHED|wc -l
```

4) 查出哪个IP地址连接最多,将其封了.

```shell
netstat -na|grep ESTABLISHED|awk {print $5}|awk -F: {print $1}|sort|uniq -c|sort -r +0n

netstat -na|grep SYN|awk {print $5}|awk -F: {print $1}|sort|uniq -c|sort -r +0n
```
---------------------------------------------------------------------------------------------

## 其他

1、查看apache当前并发访问数：

```shell
netstat -an | grep ESTABLISHED | wc -l
```

对比httpd.conf中MaxClients的数字差距多少。

2、查看有多少个进程数：

```shell
ps aux|grep httpd|wc -l
```

3、可以使用如下参数查看数据

```shell
ps -ef|grep httpd|wc -l
```

统计httpd进程数，连个请求会启动一个进程，使用于Apache服务器。
表示Apache能够处理1388个并发请求，这个值Apache可根据负载情况自动调整。

```shell
netstat -nat|grep -i "80"|wc -l
```

netstat -an会打印系统当前网络链接状态，而grep -i "80"是用来提取与80端口有关的连接的，wc -l进行连接数统计。
最终返回的数字就是当前所有80端口的请求总数。

```shell
netstat -na|grep ESTABLISHED|wc -l
```

netstat -an会打印系统当前网络链接状态，而grep ESTABLISHED 提取出已建立连接的信息。 然后wc -l统计。
最终返回的数字就是当前所有80端口的已建立连接的总数。

```shell
netstat -nat||grep ESTABLISHED|wc - # 可查看所有建立连接的详细记录
```
查看Apache的并发请求数及其TCP连接状态：

```shell
netstat -n | awk '/^tcp/ {++S[$NF]} END {for(a in S) print a, S[a]}'
```

**执行结果**

```
TIME_WAIT 8947 等待足够的时间以确保远程TCP接收到连接中断请求的确认
FIN_WAIT1 15 等待远程TCP连接中断请求，或先前的连接中断请求的确认
FIN_WAIT2 1 从远程TCP等待连接中断请求
ESTABLISHED 55 代表一个打开的连接
SYN_RECV 21 再收到和发送一个连接请求后等待对方对连接请求的确认
CLOSING 2 没有任何连接状态
LAST_ACK 4 等待原来的发向远程TCP的连接中断请求的确认
```

## TCP连接状态详解 

|状态|描述|
|:---:|:---|
|LISTEN|侦听来自远方的TCP端口的连接请求|
|SYN-SENT|再发送连接请求后等待匹配的连接请求|
|SYN-RECEIVED|再收到和发送一个连接请求后等待对方对连接请求的确认|
|ESTABLISHED|代表一个打开的连接|
|FIN-WAIT-1|等待远程TCP连接中断请求，或先前的连接中断请求的确认|
|FIN-WAIT-2|从远程TCP等待连接中断请求|
|CLOSE-WAIT|等待从本地用户发来的连接中断请求|
|CLOSING|等待远程TCP对连接中断的确认|
|LAST-ACK|等待原来的发向远程TCP的连接中断请求的确认|
|TIME-WAIT|等待足够的时间以确保远程TCP接收到连接中断请求的确认|
|CLOSED|没有任何连接状态|

---------------------------------------------------------------------------------------------

如发现系统存在大量TIME_WAIT状态的连接，通过调整内核参数解决

```shell
vim /etc/sysctl.conf
```

编辑文件，加入以下内容：

```
net.ipv4.tcp_syncookies = 1
net.ipv4.tcp_tw_reuse = 1
net.ipv4.tcp_tw_recycle = 1
net.ipv4.tcp_fin_timeout = 30
```

然后执行 `/sbin/sysctl -p` 让参数生效。

```
net.ipv4.tcp_syncookies = 1 表示开启SYN cookies。当出现SYN等待队列溢出时，启用cookies来处理，可防范少量SYN攻击，默认为0，表示关闭；
net.ipv4.tcp_tw_reuse = 1 表示开启重用。允许将TIME-WAIT sockets重新用于新的TCP连接，默认为0，表示关闭；
net.ipv4.tcp_tw_recycle = 1 表示开启TCP连接中TIME-WAIT sockets的快速回收，默认为0，表示关闭。
net.ipv4.tcp_fin_timeout 修改系統默认的 TIMEOUT 时间
```

[转自:查看linux中的TCP连接数](https://blog.csdn.net/he_jian1/article/details/40787269)
