查看mysql版本

方法一：status;

方法二：select version\(\);



Mysql启动、停止、重启常用命令

启动方式

使用 service 启动：

\[root@localhost /\]\# service mysqld start \(5.0版本是mysqld\)

\[root@szxdb etc\]\# service mysql start \(5.5.7版本是mysql\)



使用 mysqld 脚本启动：

/etc/inint.d/mysqld start



使用 safe\_mysqld 启动：

safe\_mysqld&



停止

使用 service 启动：

service mysqld stop



使用 mysqld 脚本启动：

/etc/inint.d/mysqld stop



mysqladmin shutdown



重启

使用 service 启动：

service mysqld restart 

service mysql restart \(5.5.7版本命令\)



使用 mysqld 脚本启动：

/etc/init.d/mysqld restart

