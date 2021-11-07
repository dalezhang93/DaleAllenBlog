1.下载
curl -O https://cdn.mysql.com//Downloads/MySQL-8.0/mysql-8.0.20-linux-glibc2.12-x86_64.tar.xz

curl -O https://cdn.mysql.com/archives/mysql-8.0/mysql-8.0.17.tar.gz

2.解压

`tar xvf mysql-8.0.20-linux-glibc2.12-x86_64.tar.xz`

3.移动到/usr/local/mysql

`mv mysql-8.0.20-linux-glibc2.12-x86_64 /usr/local/mysql`

4.配置mysql

vi /etc/my.cnf

```
[mysqld]
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
socket=/usr/local/mysql/data/mysql.sock

// 必须在初始化之前设定该参数,mysql8+
lower_case_table_names=1

// 密码在这儿
log_error=/usr/local/mysql/sql_log/mysql-error.log

[client]
socket=/usr/local/mysql/data/mysql.sock

[mysql]
socket=/usr/local/mysql/data/mysql.sock
```

5.创建配置文件用到的目录

```
cd /usr/local/mysql
mkdir data sql_log undo
```

6.创建用户

`adduser mysql`

7.赋权

```
chown mysql:mysql -R data sql_log undo
chmod -R 777 data sql_log undo
```

8.配置MySQL环境变量

`export PATH=$PATH:/usr/local/mysql/bin`

9.初始化MySQL

`/usr/local/mysql/bin/mysqld --initialize --user=mysql --basedir=/usr/local/mysql --datadir=/usr/local/mysql/data`

--user=mysql：初始化用户
--basedir=/usr/local/mysql：MySQL目录
--datadir=/usr/local/mysql/data：MySQL数据存放目录

进入data目录，查看是否有文件,有文件就是启动成功

10.预备启动MySQL

`cp /usr/local/mysql/support-files/mysql.server /etc/init.d/mysqld`

support-files/mysql.server：mysql启动脚本
/etc/init.d/：用来存放服务脚本，可以通过service xxx start来启动

11.启动、停止、重启MySQL

service mysqld start|stop|restart
或者
/etc/init.d/mysqld start|stop|restart

12.查看MySQL初始化密码

记录在 `sql_log` 的 `mysql_error.log` 中

`grep sql_log/password mysql-error.log`

13.登录修改密码

```
ALTER USER 'root'@'localhost' IDENTIFIED WITH mysql_native_password BY '密码'; 

update mysql.user set host ='%' where user='root';

flush privileges;
```

