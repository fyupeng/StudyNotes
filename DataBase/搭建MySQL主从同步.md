搭建MySQL主从同步

## 1. 数据库的下载

### 1.1 准备

先创建一个存放压缩包的文件夹，推荐在`/opt/sofware/`

```ruby
[root@ccna093cp4qbc64l local]# mkdir /opt/software
[root@ccna093cp4qbc64l local]# ls
bin  ctcloud  etc  games  include  lib  lib64  libexec  sbin  share  src
[root@ccna093cp4qbc64l local]# cd /opt/software/
[root@ccna093cp4qbc64l software]# ls
```

### 1.2 下载和解压

- 下载

接着，拉取 `Mysql8.0.25` 压缩包，我的压缩包支持通用 `Linux`

```ruby
wget https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz
```

下载成功結果

```ruby
--2023-02-21 11:02:14--  https://downloads.mysql.com/archives/get/p/23/file/mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz
Resolving downloads.mysql.com (downloads.mysql.com)... 104.71.161.87, 2600:1417:e800:189::2e31, 2600:1417:e800:18a::2e31
Connecting to downloads.mysql.com (downloads.mysql.com)|104.71.161.87|:443... connected.
HTTP request sent, awaiting response... 302 Moved Temporarily
Location: https://cdn.mysql.com/archives/mysql-8.0/mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz [following]
--2023-02-21 11:02:19--  https://cdn.mysql.com/archives/mysql-8.0/mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz
Resolving cdn.mysql.com (cdn.mysql.com)... 23.203.29.47
Connecting to cdn.mysql.com (cdn.mysql.com)|23.203.29.47|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 896219776 (855M) [text/plain]
Saving to: ‘mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz’

100%[==================================================================================>] 896,219,776 15.1MB/s   in 61s    

2023-02-21 11:03:21 (14.0 MB/s) - ‘mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz’ saved [896219776/896219776]
[root@ccna093cp4qbc64l software]# ls
mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz
```

- 解压

```ruby
[root@ccna093cp4qbc64l software]# tar xf mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz 
[root@ccna093cp4qbc64l software]# ls
mysql-8.0.25-linux-glibc2.12-x86_64  mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz
```

解压后转移到`/usr/local/mysql-8.0.25`目录下

```ruby
[root@ccna093cp4qbc64l software]# ls
mysql-8.0.25-linux-glibc2.12-x86_64  mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz
[root@ccna093cp4qbc64l software]# mv mysql-8.0.25-linux-glibc2.12-x86_64 /usr/local/
bin/     ctcloud/ etc/     games/   include/ lib/     lib64/   libexec/ sbin/    share/   src/     
[root@ccna093cp4qbc64l software]# mv mysql-8.0.25-linux-glibc2.12-x86_64 /usr/local/mysql-8.0.25
[root@ccna093cp4qbc64l software]# cd /usr/local/
[root@ccna093cp4qbc64l local]# ls
bin  ctcloud  etc  games  include  lib  lib64  libexec  mysql-8.0.25  sbin  share  src
[root@ccna093cp4qbc64l local]# 
```

如果出现

```ruby
xz: mysql-8.0.25-linux-glibc2.12-x86_64.tar.xz: Compressed data is corrupt
```

一般是压缩包有问题，重新下载吧

## 2. 数据库的配置

### 2.1 数据转移

为了安全性考虑，将默认集成在`MySQL`安装目录的数据文件抽取到自定义路径下

```ruby
[root@ccna093cp4qbc64l software]# mkdir /data
[root@ccna093cp4qbc64l software]# cd /data/
[root@ccna093cp4qbc64l data]# ls
[root@ccna093cp4qbc64l data]# mkdir mysql_data
[root@ccna093cp4qbc64l data]# ls
mysql_data
```

创建完成后，返回上一层目录，创建一个用户组和用户名，赋予该文件夹权限，降低文件夹访问权限

```ruby
[root@ccna093cp4qbc64l data]# ll
total 0
drwxr-xr-x 4 root root 29 Feb 21 11:14 mysql_data
[root@ccna093cp4qbc64l data]# mkdir mysql_data/{data,log}
[root@ccna093cp4qbc64l data]# ls mysql_data/
data  log
[root@ccna093cp4qbc64l data]# groupadd mysql
[root@ccna093cp4qbc64l data]# useradd -g mysql mysql
```

**命令解释：**
`useradd`：添加用户，比如`root`用户
`-g mysql`：`-g`顾名思义`group`的首字母，即添加的用户属于`mysql`用户组，`mysql`是上一步创建的用户组
`mysql`：添加的用户名

```ruby
[root@ccna093cp4qbc64l data]# ls
mysql_data
[root@ccna093cp4qbc64l data]# chown -R mysql.mysql mysql_data/ //-R 递归 reverse 子文件夹
[root@ccna093cp4qbc64l data]# ll
total 0
drwxr-xr-x 4 mysql mysql 29 Feb 21 11:14 mysql_data
[root@ccna093cp4qbc64l data]# 
```

**命令解释：**

`-R`：处理指定目录及其子目录下所有文件
`mysql.mysql`：用户组.用户

### 2.3 数据初始化

先不配置文件，先让它初始化

```ruby
[root@ccna093cp4qbc64l mysql-8.0.25]# ./bin/mysqld --user=mysql --basedir=/usr/local/mysql-8.0.25/ --datadir=/data/mysql_data/data --initialize
```

不出意外的话，应该会报错

```bash
./bin/mysqld: error while loading shared libraries: libaio.so.1: cannot open shared object file: No such file or directory
1
```

在`linux`上安装`mysql`的时候出现错误，意思就是缺少`libaio`这个依赖包。

- `ubuntu`版本:

```bash
sudo apt-get install libaio-dev
1
```

- `CentOS`或者`redcat`版本

```bash
yum install -y libaio
```

依赖完成后，这里要提示一点，初始化的数据文件夹中不能有任何文件，而且你执行初始化的用户必须对这个文件夹有操作权限。

因为我初始化指定了`--user=mysql`，它初始化创建的数据文件将都赋有`mysql`用户权限，则`mysqld`进程只能访问赋有`mysql`权限的文件。

所以后面配置日志路径时，要看看你当前创建日志文件夹是否是`mysql`用户，否则后面使用到完整配置文件时数据库启动将不成功。

这里先创建该日志文件。

```ruby
[root@ccna093cp4qbc64l mysql_data]# systemctl start mysql
Job for mysql.service failed because the control process exited with error code. See "systemctl status mysql.service" and "journalctl -xe" for details.
[root@ccna093cp4qbc64l mysql_data]# ls
data
[root@ccna093cp4qbc64l mysql_data]# mkdir log
[root@ccna093cp4qbc64l mysql_data]# touch log/mysql-error.log
[root@ccna093cp4qbc64l mysql_data]# ll log/
total 0
-rw-r--r-- 1 root root 0 Feb 21 16:55 mysql-error.log
[root@ccna093cp4qbc64l mysql_data]# chown -R mysql.mysql log/
[root@ccna093cp4qbc64l mysql_data]# ll log/
total 0
-rw-r--r-- 1 mysql mysql 0 Feb 21 16:55 mysql-error.log
[root@ccna093cp4qbc64l log]# systemctl start mysql
[root@ccna093cp4qbc64l log]#
[root@ccna093cp4qbc64l log]# service mysql status
 SUCCESS! MySQL running (10060)
```

失败的原因是日志文件必须存在，`MySQL`数据库不会自己创建，需要我们手动创建。

```ruby
[root@ccna093cp4qbc64l data]# cd /usr/local/mysql-8.0.25/
[root@ccna093cp4qbc64l mysql-8.0.25]# ./bin/mysqld --user=mysql --basedir=/usr/local/mysql-8.0.25/ --datadir=/data/mysql_data --initialize
2023-02-21T03:41:03.991878Z 0 [Warning] [MY-011070] [Server] 'Disabling symbolic links using --skip-symbolic-links (or equivalent) is the default. Consider not using this option as it' is deprecated and will be removed in a future release.
2023-02-21T03:41:03.992034Z 0 [System] [MY-013169] [Server] /usr/local/mysql-8.0.25/bin/mysqld (mysqld 8.0.25) initializing of server in progress as process 11851
2023-02-21T03:41:04.001657Z 1 [System] [MY-013576] [InnoDB] InnoDB initialization has started.
2023-02-21T03:41:05.855231Z 1 [System] [MY-013577] [InnoDB] InnoDB initialization has ended.
2023-02-21T03:41:07.987931Z 6 [Note] [MY-010454] [Server] A temporary password is generated for root@localhost: G:oV9fas,lGg
```

我们不用急着先去改密码，我们应该测试数据库能否正常登录

### 2.4 创建软连接

- 创建服务端软连接

```ruby
[root@ccna093cp4qbc64l etc]# cd /usr/local/mysql-8.0.25/
[root@ccna093cp4qbc64l mysql-8.0.25]# cp -a /usr/local/mysql-8.0.25/support-files/mysql
mysqld_multi.server  mysql-log-rotate     mysql.server         
[root@ccna093cp4qbc64l mysql-8.0.25]# cp -a /usr/local/mysql-8.0.25/support-files/mysql.server /etc/init.d/mysql
[root@ccna093cp4qbc64l mysql-8.0.25]# chkconfig --add mysql
[root@ccna093cp4qbc64l mysql-8.0.25]# chkconfig --list

Note: This output shows SysV services only and does not include native
      systemd services. SysV configuration data might be overridden by native
      systemd configuration.

      If you want to list systemd services use 'systemctl list-unit-files'.
      To see services enabled on particular target use
      'systemctl list-dependencies [target]'.

mysql          	0:off	1:off	2:on	3:on	4:on	5:on	6:off
netconsole     	0:off	1:off	2:off	3:off	4:off	5:off	6:off
network        	0:off	1:off	2:on	3:on	4:on	5:on	6:off
```

**命令解释：**
`cp -a`：归档复制，常用于备份
将`mysql.server`复制到`/etc/init.d/`目录下并更名为`mysql`

如果系统服务级别与我不同，手动设置

```ruby
chkconfig --level 2345 mysql on
```

如果`/etc/init.d/`目录中`mysql`服务是非可执行文件，赋予它权限

```ruby
chmod +x /etc/init.d/mysql 
```

**命令解释：**
`chmod +x`：将普通文件变为可执行文件【变绿】
`chmod -x`：将可执行文件改成普通文件

其他设置：

```ruby
chkconfig --add mysql
chkconfig --del mysql
chkconfig --level 2345 mysql on # 缺省 --level: chkconfig mysql on 表示默认开启级别 2,3,4,5
# 这种方式 与 systemctl enable mysql 跟 systemctl disable mysql 效果是一样的
```

现在先不要启动，编辑初始化生成的`/etc/my.cnf`，如没有自己创建即可。

```cnf
# my.cnf

[mysqld]
basedir=/usr/local/mysql-8.0.25
datadir=/data/mysql_data/data
socket=/tmp/mysql.sock
character-set-server=utf8
```

只配置简单的持久化路径，算是让启动生效的最少配置了。

之前我们已经将服务交给`chkconfig`管理了，我们可以使用`systemctl`来启动、查看状态、关闭了。

```ruby
systemctl start mysql
systemctl status mysql
systemctl stop mysql
# 兼容 之前的启动方式
service mysql start
service mysql status
service mysql stop
```

为什么说是软连接，因为它这种管理方式，也是启动了`/etc/rc*.d`下的软连接服务了，`x` 对应管理级别

```ruby
[root@ccna093cp4qbc64l rc.d]# cd /etc/rc.d/
[root@ccna093cp4qbc64l rc.d]# ll 
total 4
drwxr-xr-x. 2 root root  96 Feb 21 12:38 init.d
drwxr-xr-x. 2 root root  61 Feb 21 12:38 rc0.d
drwxr-xr-x. 2 root root  61 Feb 21 12:38 rc1.d
drwxr-xr-x. 2 root root  61 Feb 21 12:38 rc2.d
drwxr-xr-x. 2 root root  61 Feb 21 12:38 rc3.d
drwxr-xr-x. 2 root root  61 Feb 21 12:38 rc4.d
drwxr-xr-x. 2 root root  61 Feb 21 12:38 rc5.d
drwxr-xr-x. 2 root root  61 Feb 21 12:38 rc6.d
-rwxr-xr-x. 1 root root 529 Sep  7  2020 rc.local
```

当中，文件首个字母为`S`或者为`K`表示`Start`和`Kill`动作，对应级别下的`on`跟`off`。

如果启动失败，检查启动进程具有的权限是不是满足不低于数据、日志文件赋予的权限。

数据库服务器可以在`my.cnf`中指定以哪一个用户运行

```cnf
 [mysqld]
 user=mysql
```

- 创建客户端软连接

```ruby
[root@ccna093cp4qbc64l mysql-8.0.25]# cd /usr/local/mysql-8.0.25/
[root@ccna093cp4qbc64l mysql-8.0.25]# ls
bin  docs  include  lib  LICENSE  man  README  share  support-files
[root@ccna093cp4qbc64l mysql-8.0.25]# ln -s /usr/local/mysql-8.0.25/bin/mysql /usr/bin/
[root@ccna093cp4qbc64l mysql-8.0.25]# ll /usr/bin/mysql 
lrwxrwxrwx 1 root root 33 Feb 21 11:28 /usr/bin/mysql -> /usr/local/mysql-8.0.25/bin/mysql
```

这样，软连接就生效了，因为`/usr/bin`中的可执行文件是全局性的。

可以执行以下登录客户端:

```ruby
mysql -uroot -p
```

### 2.5 修改密码

客户端启动成功后第一步必须修改密码，否则任何操作都被拒绝。

```sql
mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '新密码';
```

### 2.6 访问授权

- 本地密码访问权限修改

其实我们默认密码`plugin`都是`caching_sha2_password`，只是上一步被我们改成了`mysql_native_password`。

主要是为了`navicate`做兼容。

```ruby
mysql> select user,host,plugin from user;
+------------------+-----------+-----------------------+
| user             | host      | plugin                |
+------------------+-----------+-----------------------+
| mysql.infoschema | localhost | caching_sha2_password |
| mysql.session    | localhost | caching_sha2_password |
| mysql.sys        | localhost | caching_sha2_password |
| root             | localhost | mysql_native_password |
+------------------+-----------+-----------------------+
4 rows in set (0.00 sec)

mysql> ALTER USER 'root'@'localhost' IDENTIFIED WITH caching_sha2_password BY 'root';  
Query OK, 0 rows affected (0.02 sec)

mysql> select user,host,plugin from user;
+------------------+-----------+-----------------------+
| user             | host      | plugin                |
+------------------+-----------+-----------------------+
| mysql.infoschema | localhost | caching_sha2_password |
| mysql.session    | localhost | caching_sha2_password |
| mysql.sys        | localhost | caching_sha2_password |
| root             | localhost | caching_sha2_password |
+------------------+-----------+-----------------------+
```

- 外网访问授权

```ruby
mysql> select user,host,plugin from user;
+------------------+-----------+-----------------------+
| user             | host      | plugin                |
+------------------+-----------+-----------------------+
| root             | %         | caching_sha2_password |
| mysql.infoschema | localhost | caching_sha2_password |
| mysql.session    | localhost | caching_sha2_password |
| mysql.sys        | localhost | caching_sha2_password |
| root             | localhost | caching_sha2_password |
+------------------+-----------+-----------------------+
5 rows in set (0.00 sec)
```

修改`plugin`属性

```mysql
mysql>ALTER USER 'root'@'%' IDENTIFIED WITH caching_sha2_password BY '你的密码';  
```

授予访问权限

```mysql
mysql> grant all privileges on *.* to root@'%' with grant option;
mysql> flush privileges;
# 回收权限
mysql> # revoke all privileges,grant option from 'root'@”%”;
```

再对`mysql`对应的用户设置成`nologin`模式

```ruby
[root@ccna093cp4qbc64l ~]# su mysql
[mysql@ccna093cp4qbc64l root]$ su root
Password: 
[root@ccna093cp4qbc64l ~]# usermod -s /sbin/nologin mysql
[root@ccna093cp4qbc64l ~]# su mysql
This account is currently not available.
```

### 2.2 配置

这一步涉及到权限问题，很容易导致数据库启动失败，一步一步来，绝对没有问题。

```cnf
# my.cnf

# MySQL 配置文件，
#
# # 数据库目录 /data/mysql
 [client]
 port=3306
 # mysql socket 文件存放地址
 socket=/tmp/mysql.sock
 # 默认字符集
 default-character-set=utf8

 [mysqld]
 server-id=1
 # 端口
 port=3306
 # 运行用户，这个用户是指服务器(mysqld)进程以这个用户启动，只具有这个用户权限
 user=mysql
 # 最大连接
 max_connections=200
 socket=/tmp/mysql.sock
 # mysql 安装目录（解压后文件的目录）
 basedir=/usr/local/mysql-8.0.25
 # 数据目录（这里放在我们新建的 /data/mysql 下）
 datadir=/data/mysql_data/data
 pid-file=/data/mysql_data/data/mysql.pid
 init-connect='SET NAMES utf8'
 character-set-server=utf8
 # 数据库引擎
 default-storage-engine=INNODB
 slow_query_log_file=/data/mysql_data/log/mysql-slow.log
 # 远程连接访问,低版navicate需要该注解
 #default_authentication_plugin=mysql_native_password

 # 跳过验证密码
 #skip-grant-tables

 [mysqld_safe]
 log_error=/data/mysql_data/log/mysql-error.log

 [mysqldump]
 quick
 max_allowed_packet=16M
 EOF
```

### 2.3 免密安全登录

- 前台启动

它会帮你启动`mysqld`同时启动`mysqld_safe`这两个进程。

```ruby
[root@ccna093cp4qbc64l bin]# ./mysqld_safe --skip_grant_tables
2023-02-21T09:11:56.700648Z mysqld_safe Logging to '/data/mysql_data/log/mysql-error.log'.
2023-02-21T09:11:56.737520Z mysqld_safe Starting mysqld daemon with databases from /data/mysql_data/data

^Z
[1]+  Stopped                 ./mysqld_safe --skip_grant_tables
[root@ccna093cp4qbc64l bin]# bg
[1]+ ./mysqld_safe --skip_grant_tables &
[root@ccna093cp4qbc64l bin]# mysql -uroot -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
.....
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> exit
Bye
[root@ccna093cp4qbc64l bin]# 
```

- 后台启动

```ruby
[root@ccna093cp4qbc64l bin]# nohup ./mysqld_safe --skip_grant_tables &
[1] 13132
[root@ccna093cp4qbc64l bin]# nohup: ignoring input and appending output to ‘nohup.out’

[root@ccna093cp4qbc64l bin]# ps -ef | grep mysql
root     13132  6069  0 17:18 pts/2    00:00:00 /bin/sh ./mysqld_safe --skip_grant_tables
mysql    13394 13132  9 17:18 pts/2    00:00:01 /usr/local/mysql-8.0.25/bin/mysqld --basedir=/usr/local/mysql-8.0.25 --datadir=/data/mysql_data/data --plugin-dir=/usr/local/mysql-8.0.25/lib/plugin --user=mysql --skip-grant-tables --log-error=/data/mysql_data/log/mysql-error.log --pid-file=/data/mysql_data/data/mysql.pid --socket=/tmp/mysql.sock --port=3306
root     13490  6069  0 17:18 pts/2    00:00:00 grep --color=auto mysql
```

## 3. 主从同步搭建

### 3.1 环境准备

- 主库配置

```cnf
 # [mysqld] 下增加以下或修改
 [mysqld]
 server-id=1 # 唯一识别 id
 log-bin=mysql-bin
 # 中继日志
 relay-log=relay-log
 relay_log_index=relay-log.index
```

重启后验证下

```sql
mysql> show global variables like "%log_bin%";
```

- 从库配置

```cnf
 # [mysqld] 下增加以下或修改
 [mysqld]
 server-id=2 # 唯一识别 id
 # 忽略表
 replicate-wild-ignore-table=mysql.*
 replicate-wild-ignore-table=sys.*
 # 主从同步中, 不需要开启 二进制日志
 skip-log-bin # 跳过 默认开启二进制，5.0 版本只需要注释 下一行即可，8.0 缺省默认开启并且以 binlog.000** 命名， 关闭要写上
 #log-bin=mysql-bin 
```

现在要将主数据库数据备份一份出来，因为要主从同步，到时候导入到从服务器中，备份的时候将这个操作设置为一个事务

```sql
mysql> flush tables with read lock;
```

导出备份数据，注意当前路径在`/usr/local/mysql-8.0.25/bin/`下，连接套接字文件`mysql.sock`路径在`my.cnf`所配置路径中。

```ruby
[root@ccna093cp4qbc64l bin]# ./mysqldump --no-defaults  -uroot -p'******' -S /tmp/mysql.sock --all-databases > /data/mysql_data/mysql_bak.$(date +%F).sql
mysqldump: [Warning] Using a password on the command line interface can be insecure.
```

数据文件过大考虑压缩备份

```ruby
[root@ccna093cp4qbc64l bin]# ./mysqldump --no-defaults  -uroot -p'******' -S /tmp/mysql.sock --all-databases | gzip > /data/mysql_data/mysql_bak.$(date +%F).sql.gz
mysqldump: [Warning] Using a password on the command line interface can be insecure.
```

事务完成了，解锁恢复其他事务的写操作

```sql
mysql> unlock tables;
```

导入从数据库

```ruby
[root@ccna093cp4qbc64l backup]# mysql -uroot -p'1305820000103FYp' -S /tmp/mysql.sock < mysql_bak.2023-02-22.sql
mysql: [Warning] Using a password on the command line interface can be insecure.
```

### 3.2 同步准备

- 定位主数据库同步位置

```sql
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |      824 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

- 开始同步

最关键的一步，在从数据库执行

```sql
CHANGE MASTER TO 
MASTER_HOST='47.107.63.171', # 主从不在同一网段，可以配公网，主数据库提供 同步的用户至少 rep@'本机IP' 或 rep@'%'
MASTER_USER='rep', 
MASTER_PASSWORD='123456', 
MASTER_LOG_FILE='master-log-bin.000003', # 对应持久化数据文件位置，一般由 mysql-bin.** 和 mysql-bin.index 控制
MASTER_LOG_POS=1282;
```

其实主从同步中先会去找`mysql-bin.index`该索引文件。文件内容即是二进制日志文件的文件名，是这样定位的。

配置完成后，开启同步

```sql
mysql> start slave;
```

然后查看是否已经启动成功

```sql
mysql> show slave status \G;
*************************** 1. row ***************************
               Slave_IO_State: Waiting for master to send event
                  Master_Host: 47.107.63.171
                  Master_User: rep
                  Master_Port: 3306
                Connect_Retry: 60
              Master_Log_File: mysql-bin.000001
          Read_Master_Log_Pos: 1282
               Relay_Log_File: ccna093cp4qbc64l-relay-bin.000002
                Relay_Log_Pos: 324
        Relay_Master_Log_File: mysql-bin.000001
             Slave_IO_Running: Yes
            Slave_SQL_Running: Yes
```

否则先进行`stop slave`，执行不了说明`start slave`启动没有成功，也就是需要重新在`3.2`章节配置正确。

## 4. 主从测试

### 4.1 测试DDL

- 主库操作

主库先定位到一个`database`，查看所有`table`

```sql
mysql> use java_training 
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-------------------------+
| Tables_in_java_training |
+-------------------------+
| my_order                |
| user                    |
+-------------------------+
2 rows in set (0.00 sec)

```

主库再创建一个不存在的表

```sql

mysql> create table rep_table(id varchar(16) primary key, rep_name varchar(20) not null) engine=innodb default charset=utf8;
Query OK, 0 rows affected, 1 warning (0.03 sec)

```

- 从库查询

从库定位与主库相同到`database`，查询所有`table`

```sql
mysql> use java_training 
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
mysql> show tables;
+-------------------------+
| Tables_in_java_training |
+-------------------------+
| my_order                |
| rep_table               |
| user                    |
+-------------------------+
3 rows in set (0.01 sec)

```

### 4.2 测试DML

- 从库查询

```sql
mysql> select * from rep_table;
Empty set (0.00 sec)
```

- 主库操作

```sql
mysql> insert into rep_table(id, rep_name) values('1', 'fyupeng');
Query OK, 1 row affected (0.01 sec)
```

- 从库验证

```sql

mysql> select * from rep_table;
+----+----------+
| id | rep_name |
+----+----------+
| 1  | fyupeng  |
+----+----------+
1 row in set (0.00 sec)
```

### 4.3 状态验证

`Position`由`1282` 变为`1892`了。

```sql
mysql> show master status;
+------------------+----------+--------------+------------------+-------------------+
| File             | Position | Binlog_Do_DB | Binlog_Ignore_DB | Executed_Gtid_Set |
+------------------+----------+--------------+------------------+-------------------+
| mysql-bin.000001 |     1892 |              |                  |                   |
+------------------+----------+--------------+------------------+-------------------+
1 row in set (0.00 sec)
```

## 5. 数据不一致异常

数据不一致导致主从同步失败，主要从两个方面讨论。

### 5.1 IO线程异常

恢复备份数据后，重置从服务器配置，会导致拥有主从同步权限的用户失效，需要重新更新。

- 主服务器`IP`对从服务器不可达
- 端口防火墙未开放给从服务器
- 二进制文件指定名称不存在

配置正确后即可解决

### 5. SQL线程异常

主从二进制日志数据不一致，从服务器宕机导致从服务器无法同步主服务器。

恢复日志备份，或让从服务器设置为主服务器，双方同步后即可实现最终同步。

- 查看主服务器日志更新位置，重新让从服务器`Change to`到主服务器的位置。

  - 主服务器开启读锁，保证此时从服务器重置后的位置与主服务器同步
  - 从服务器停止同步并重置`slave`
  - 查看主服务器当前日志文件名和位置，从服务器手动`Change to`与主服务器同步
  - 从服务器开启同步，接着解锁主服务器所有表

