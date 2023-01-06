**安装**：

```shell
sudo apt update
sudo apt install redis-server
```

**配置**：

```shell
sudo vim /etc/redis/redis.conf
```

在文件中，找到supervised指令。 该指令允许您声明一个init系统来管理Redis作为服务，从而为您提供对其操作的更多控制。 受supervised指令默认设置为no 。 由于您正在运行使用systemd init系统的Ubuntu，请将其更改为systemd ：

```
supervised systemd
```

重新加载Redis服务文件以反映您对配置文件所做的更改：

```shell
sudo service redis restart
```

查看 Redis 的运行状态：

```shell
sudo systemctl status redis
```

输出如下表示成功：

```
● redis-server.service - Advanced key-value store
   Loaded: loaded (/lib/systemd/system/redis-server.service; enabled; vendor preset: enabled)
   Active: active (running) since Thu 2022-11-24 10:59:14 CST; 15s ago
     Docs: http://redis.io/documentation,
           man:redis-server(1)
  Process: 19015 ExecStop=/bin/kill -s TERM $MAINPID (code=exited, status=0/SUCCESS)
  Process: 19019 ExecStart=/usr/bin/redis-server /etc/redis/redis.conf (code=exited, status=0/SUCCESS)
 Main PID: 19032 (redis-server)
    Tasks: 4 (limit: 2335)
   CGroup: /system.slice/redis-server.service
           └─19032 /usr/bin/redis-server 127.0.0.1:6379

Nov 24 10:59:14 Small-Fish systemd[1]: Starting Advanced key-value store...
Nov 24 10:59:14 Small-Fish systemd[1]: redis-server.service: Can't open PID file /var/run/redis/redis-server.pid (yet?) after start: No such file or directory
Nov 24 10:59:14 Small-Fish systemd[1]: Started Advanced key-value store.
```

### redis 服务控制

启动 redis 服务：

```
$ sudo service redis start
```

关闭 redis 服务：

```
$ sudo service redis stop
```

重启 redis 服务：

```
$ sudo service redis restart
```

### redis 客户端连接

在命令行中输入如下命令来登陆进 redis 客户端

```
$ redis-cli
```

### redis 远程连接

打开配置文件

```
$ sudo vi /etc/redis/redis.conf
```

将 bind 127.0.0.1 ::1 改为 bind 0.0.0.0

之后重新启动 redis

### Redis 设置密码

打开 Redis 的配置文件

```
$ sudo vi /etc/redis/redis.conf
```

找到下面这一行

```
# requirepass foobared 
```

将注释符号去掉，将后面修改成自己的密码，如:

```
requirepass 123456
```