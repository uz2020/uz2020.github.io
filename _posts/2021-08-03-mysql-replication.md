---
layout: post
---

我是用docker来测试的。

## docker配置

master：

```
# Use root/example as user/password credentials
version: '3.1'

services:

  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 1
    ports:
      - 172.20.0.1:3306:3306

  adminer:
    image: adminer
    restart: always
    ports:
      - 172.20.0.1:8080:8080
```

slave：

```
# Use root/example as user/password credentials
version: '3.1'

services:

  db:
    image: mysql
    command: --default-authentication-plugin=mysql_native_password
    restart: always
    environment:
      MYSQL_ROOT_PASSWORD: 1
    ports:
      - 172.19.0.1:3306:3306

  adminer:
    image: adminer
    restart: always
    ports:
      - 172.19.0.1:8080:8080
```

还不清楚docker的虚拟网卡的工作原理，只是看到它创建了两个网卡，对应于两个网段，所以就用来让master和slave作为服务器地址绑定了。当然也可以映射不同的端口到本地，但这样随机选择一个端口很容易造成混乱。

## conf.d

master：

```
[mysqld]
log-bin = master-bin
log-bin-index = master-bin.index
server-id = 1
```

slave：

```
[mysqld]
relay-log = slave-relay-bin
relay-log-index = slave-relay-bin.index
server-id = 2
```

mysql的官方docker容器里头不能编辑文件，目前我是通过docker cp的方式把配置文件放入容器。

## 创建repl用户

由于我手头的书很旧，它给出的命令是MySQL 5.1的。所以去找了最新的MySQL 8.0 manual来看。

在master上创建用户： （MySQL 5.1里identified by这一段是放在grant语句里的）

```sql
create user repl identified by '1';
grant replication slave on *.* to repl;
```

在slave上连接到master：

```sql
change master to
master_host = '172.20.0.1',
master_port = 3306,
master_user = 'repl',
master_password = '1';

start slave
```

完成配置。现在在master上创建数据库、表、数据都会同步到slave上。但是，如果在slave上有修改的操作，并不会同步到master。
