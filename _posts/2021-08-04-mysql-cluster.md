---
layout: post
title: MySQL Cluster部署
---

## 创建一个网络

```bash
docker network create cluster --subnet=192.168.0.0/16
```

## 启动manager，管理data nodes

```bash
docker run -d --net=cluster --name=management1 --ip=192.168.0.2 mysql/mysql-cluster ndb_mgmd
```

## 启动两个data node

```bash
docker run -d --net=cluster --name=ndb1 --ip=192.168.0.3 mysql/mysql-cluster ndbd
docker run -d --net=cluster --name=ndb2 --ip=192.168.0.4 mysql/mysql-cluster ndbd
```

## 启动mysql server

```bash
docker run -d --net=cluster --name=mysql1 --ip=192.168.0.10 -e MYSQL_RANDOM_ROOT_PASSWORD=true mysql/mysql-cluster mysqld
```

这是cluster版本的mysql server。和innodb版本的不同。如下：

```
mysql> select version()\g
+----------------+
| version()      |
+----------------+
| 8.0.26-cluster |
+----------------+
1 row in set (0.00 sec)
```

## 重置密码

```bash
docker logs mysql1 2>&1 | grep PASSWORD
docker exec -it mysql1 mysql -uroot -p
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass';
```

## 使用ndb utilities: ndb_mgm

```bash
docker run -it --net=cluster mysql/mysql-cluster ndb_mgm
```
