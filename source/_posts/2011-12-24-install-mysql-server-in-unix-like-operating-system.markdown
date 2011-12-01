---
layout: post
title: "Install-Mysql-Server-In-Unix-Like-Operating-System"
date: 2011-12-24 06:08
comments: true
categories: Mysql
---

>CentOS 5

- Installing MySQL

{%codeblock%}
yum install mysql-server
/sbin/chkconfig --levels 235 mysqld on
{%endcodeblock%}

The MySQL server package will be installed on your server, **along with dependencies and client libraries.** Start MySQL by running the following command:

`service mysqld start`

- Configuring MySQL

After installing MySQL, it's recommended that you run **mysql_secure_installation**, a program that helps secure MySQL. While running mysql_secure_installation, **you will be presented with the opportunity to change the MySQL root password, remove anonymous user accounts, disable root logins outside of localhost, and remove test databases.** It is recommended that you answer yes to these options. If you are prompted to reload the privilege tables, select yes. 


By default, MySQL makes some assumptions about your server environment with respect to memory. To configure MySQL more conservatively, you'll need to edit some settings in its configuration file. Your file should resemble the following:

`File:/etc/my.cnf`

{%codeblock%}
[mysqld]
datadir=/var/lib/mysql
socket=/var/lib/mysql/mysql.sock
user=mysql
# Default to using old password format for compatibility with mysql 3.x
# clients (those using the mysqlclient10 compatibility package).
old_passwords=1

# Disabling symbolic-links is recommended to prevent assorted security risks;
# to do so, uncomment this line:
# symbolic-links=0

key_buffer = 16K
max_allowed_packet = 1M
thread_stack = 64K
table_cache = 4
sort_buffer = 64K
net_buffer_length = 2K
bind-address = 127.0.0.1

[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
These settings are only suggested values for a low memory environment; please feel free to tune them to appropriate values for your server. Consult the "More Information" section at the end of this tutorial for additional resources for this topic.
{%endcodeblock%}

If you made any changes to MySQL's configuration, issue the following command to restart it:

`service mysqld restart`

MySQL will bind to localhost (127.0.0.1) by default. Please reference our secure MySQL remote access guide for information on connecting to your databases with local clients.

Allowing unrestricted access to MySQL on a public IP not advised, but you may change the address it listens on by modifying the bind-address parameter. If you decide to bind MySQL to your public IP, **you should implement firewall rules that only allow connections from specific IP addresses.
**

- Using MySQL

To generate a list of commands for the MySQL prompt type \h:


`Note that all text commands must be first on line and end with ';'`

{%codeblock%}
?         (\?) Synonym for `help'.
clear     (\c) Clear command.
connect   (\r) Reconnect to the server. Optional arguments are db and host.
delimiter (\d) Set statement delimiter. NOTE: Takes the rest of the line as new delimiter.
edit      (\e) Edit command with $EDITOR.
ego       (\G) Send command to mysql server, display result vertically.
exit      (\q) Exit mysql. Same as quit.
go        (\g) Send command to mysql server.
help      (\h) Display this help.
nopager   (\n) Disable pager, print to stdout.
notee     (\t) Don't write into outfile.
pager     (\P) Set PAGER [to_pager]. Print the query results via PAGER.
print     (\p) Print current command.
prompt    (\R) Change your mysql prompt.
quit      (\q) Quit mysql.
rehash    (\#) Rebuild completion hash.
source    (\.) Execute an SQL script file. Takes a file name as an argument.
status    (\s) Get status information from the server.
system    (\!) Execute a system shell command.
tee       (\T) Set outfile [to_outfile]. Append everything into given outfile.
use       (\u) Use another database. Takes database name as argument.
charset   (\C) Switch to another charset. Might be needed for processing binlog with multi-byte charsets.
warnings  (\W) Show warnings after every statement.
nowarning (\w) Don't show warnings after every statement.

{%endcodeblock%}

- Resetting the MySQL Root Password
If you've forgotten your root MySQL password, you may recover it by issuing the following commands:

{%codeblock%}
/etc/init.d/mysqld stop
mysqld_safe --skip-grant-tables &
mysql -u root
{%endcodeblock%}

The following part of the password reset will now be done within the MySQL client program:

{%codeblock%}
USE mysql;
UPDATE user SET PASSWORD=PASSWORD("CHANGEME") WHERE USER='root';
FLUSH PRIVILEGES;
exit

{%endcodeblock%}

Last, restart MySQL by issuing:

{%codeblock%}
service mysqld restart
{%endcodeblock%}

>Ubuntu 10.04 LTS (Lucid)

- Installing MySQL

{%codeblock%}
apt-get install mysql-server
update-rc.d mysql defaults #Or,sysv-rc-conf
{%endcodeblock%}

- Configuring MySQL

`/etc/mysql/my.cnf `

If you made any changes to MySQL's configuration, restart it by issuing the following command:

{%codeblock%}
/etc/init.d/mysqld restart
{%endcodeblock%}

- Resetting the MySQL Root Password

{%codeblock%}
dpkg-reconfigure mysql-server-5.1
{%endcodeblock%}


> OS X

Hombrew方式

{%codeblock%}
brew install mysql
mysql_install_db
mysql.server start
{%endcodeblock%}

二进制安装包方式


更多阅读

[更改MySQL数据库root密码的具体方法](http://www.2cto.com/database/201006/50201.html "更改MySQL数据库root密码的具体方法")

[Mac OS启动服务优化高级篇（launchd tuning）](http://kenwublog.com/mac-os-launchd-tuning "Mac OS启动服务优化高级篇（launchd tuning）")

[Lingon Launchd 后台服务管理器 For Mac](http://lingon.macgood.com/ "Lingon Launchd 后台服务管理器 For Mac")
