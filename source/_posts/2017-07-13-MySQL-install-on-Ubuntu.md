---
title: MySQL install on Ubuntu
comments: false
toc: true
date: 2017-07-13 17:21:05
tags:
	- database
	- ubuntu
---

### install
``` shell
$ sudo apt-get install mysql-server mysql-client libmysqlclient-dev mysql-workbench
```

### mysql.service
```
$ sudo /etc/init.d/mysql start
$ sudo /etc/init.d/mysql stop
$ sudo /etc/init.d/mysql restart
```

### enter mysql shell
```
$ mysql -uroot -p
```

<!-- more -->

### errors
##### ERROR 1045 (28000): Access denied for user 'root'@'localhost' (using password: YES)
**problem: ** invalid password
**solution: ** use user and password in /etc/mysql/debian.cnf([client] part) to reset root password
```
$ sudo cat /etc/mysql/debian.cnf
$ mysql -udebian-sys-maint -p
mysql> use mysql;
mysql> UPDATE user SET Password=PASSWORD('new password') WHERE USER='root';
mysql> FLUSH PRIVILEGES;
mysql> quit;
```

##### ERROR 2002 (HY000): Can't connect to local MySQL server through socket '/var/run/mysqld/mysqld.sock' (2)
**problem: ** mysql.service not start
**solution: ** start mysql.service


