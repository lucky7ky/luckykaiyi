---
published: true
layout: post
title: "MySQL 存储 emoji 表情的解决方法"
date: 2017-08-26
--- 

### 问题发现

之前的个人练习项目中，写了一个小爬虫 ，主要从北邮人论坛、水木社区和 v2ex 定时抓取招聘信息，存入 MySQL 数据库， 前端展现在 [邮订阅] 上。某天打开网站的时候，看到最近都没有再更新数据了。到服务器上查错误日志和代码调试，最后定位到这个报错信息： 

```
Error: ER_TRUNCATED_WRONG_VALUE_FOR_FIELD: Incorrect string value: 
'\xF0\x9F\x91\x8D' for column 'title' at row 1
```
原来是有篇招聘信息的标题为 "xxxxxx👍", 就是最后这个 emoji 表情，导致入库失败。

### 问题原因

原因与 MySQL 中的字符集设置有关。MySQL 中常用的 utf8 编码一个字符最多3字节, 而 emoji 表情为4个字节, 所以在写入的时候会报错。从 MySQL 5.5.3 版本开始，增加了 utf8mb4 编码，能够支持最多4个字节， 而且 utf8mb4 能够向下兼容 utf8。

### 问题解决

所以，解决方案就是将 MySQL 中字符集修改为 utf8mb4。具体操作记录如下：

#### 1. 查看当前 MySQL 版本号

使用命令行模式进入 MySQL 会看到最开始的提示符 

```
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 165
Server version: 5.6.35 MySQL Community Server (GPL)
```
或者使用 status 命令

```
mysql> status;
--------------
mysql  Ver 14.14 Distrib 5.6.35, for Linux (x86_64) using  EditLine wrapper
...
```
如果当前 MySQL 版本小于5.5.3， 需要先升级到 5.5.3+ 的版本。

#### 2. 查看服务器字符集设置

```
mysql> SHOW VARIABLES WHERE Variable_name LIKE 'character%' OR
Variable_name LIKE 'collation%';
+--------------------------+----------------------------+
| Variable_name            | Value                      |
+--------------------------+----------------------------+
| character_set_client     | utf8                       |
| character_set_connection | utf8                       |
| character_set_database   | latin1                     |
| character_set_filesystem | binary                     |
| character_set_results    | utf8                       |
| character_set_server     | latin1                     |
| character_set_system     | utf8                       |
| character_sets_dir       | /usr/share/mysql/charsets/ |
| collation_connection     | utf8_general_ci            |
| collation_database       | latin1_swedish_ci          |
| collation_server         | latin1_swedish_ci          |
+--------------------------+——————————————+
```
可以看到目前的字符集设置情况。

#### 3. 修改 MySQL 配置文件 my.cnf（windows为my.ini）

```
sudo vi /etc/mysql/my.cnf
```

👇下面是网上常见的教程示例，在 `my.cnf` 中的这三个地方添加相应内容。

```
[client]
 default-character-set = utf8mb4
 
[mysql] 
 default-character-set = utf8mb4 
 
[mysqld]
 character-set-client-handshake = FALSE
 character-set-server = utf8mb4
 collation-server = utf8mb4_unicode_ci
 init_connect='SET NAMES utf8mb4'
```
但在实际操作中，打开服务器上的 `my.cnf` 时，里面并没有 **[mysql]** 这一项，而且多出了 **[mysqld_safe]** 这一项(疑惑脸🐱）。之前没印象修改过 `my.cnf`, 应该都是用的默认配置吧。参考上面的配置，查了一下 MySQL 官方文档，最后实际配置的时候没有用到 **[mysql]** 那一项， 其他的相同。

-------不专业的分割线 (这部分可以跳过🐸） -------

这里整理记录一下官网文档中相关的部分
> If an option group name is the same as a program name, options in the group apply specifically to that program. For example, the [mysqld] and [mysql] groups apply to the mysqld server and the mysql client program, respectively.



#### 👉 mysqld vs mysqld_safe

> The MySQL server, mysqld, is the main program that does most of the work in a MySQL installation. The server is accompanied by several related scripts that assist you in starting and stopping the server:

> **mysqld**   
The SQL daemon (that is, the MySQL server). To use client programs, mysqld must be running, because clients gain access to databases by connecting to the server. 

> **mysqld_safe**  
> 
> - A server startup script. mysqld_safe attempts to start mysqld. 
> - It is the recommended way to start a mysqld server on Unix. mysqld_safe adds some safety features such as restarting the server when an error occurs and logging runtime information to an error log file.
> - It reads all options from the [mysqld], [server], and [mysqld_safe] sections in option files. 


👆官网中的说明显示，**mysqld_safe** 能够读取 **[mysqld]** 中的配置。所以在`my.cnf` 中的 **[mysqld]** 部分添加的字符集设置就没必要在 **[mysqld_safe]** 中进行重复配置了。

#### 👉 client vs mysql

> **client**  
The [client] option group is read by all client programs provided in MySQL distributions.

>  **mysql**  
The command-line tool for interactively entering SQL statements or executing them from a file in batch mode.  

> NOTE: List more general option groups first and more specific groups later.Options specified later override options specified earlier.

 👆同样，如果不需要对 mysql 命令行工具进行特殊配置，就不需要在 `my.cnf` 中增加 **[mysql]** 这个 option group 了， 因为它会读取 **[client]** 中的配置。
 
🐸 好了，继续下一步...

#### 4. 重启 MySQL 数据库

改完 `my.cnf` 后，重启一下 MySQL 数据库。  
Ubuntu Server 下可以使用如下命令：

```
sudo /etc/init.d/mysql restart
```
此时再查一下服务器字符集设置。结果如下：

```
mysql> SHOW VARIABLES WHERE Variable_name LIKE 'character%' OR
Variable_name LIKE 'collation%';
+--------------------------+----------------------------+
| Variable_name | Value |
+--------------------------+----------------------------+
| character_set_client | utf8mb4 |
| character_set_connection | utf8mb4 |
| character_set_database | utf8mb4 |
| character_set_filesystem | binary |
| character_set_results | utf8mb4 |
| character_set_server | utf8mb4 |
| character_set_system | utf8 |
| character_sets_dir | /usr/share/mysql/charsets/ |
| collation_connection | utf8mb4_unicode_ci |
| collation_database | utf8mb4_unicode_ci |
| collation_server | utf8mb4_unicode_ci |
+--------------------------+----------------------------+
```
 

#### 5.修改 database、table 和 column 字符集 

##### 查看数据库字符集

```
select * from SCHEMATA where SCHEMA_NAME = 'youdingyue';
```
##### 修改数据库字符集

```
ALTER DATABASE youdingyue CHARACTER SET = utf8mb4 COLLATE = 
utf8mb4_unicode_ci;
```

##### 查看表字符集

```
select TABLE_SCHEMA,TABLE_NAME,TABLE_COLLATION from
information_schema.TABLES where TABLE_SCHEMA = 'youdingyue';
```

##### 修改表字符集

```
ALTER TABLE jobs CONVERT TO CHARACTER SET utf8mb4 COLLATE
utf8mb4_unicode_ci;
```

##### 查看列字符集

```
select TABLE_SCHEMA,TABLE_NAME,COLUMN_NAME,COLLATION_NAME from 
information_schema.COLUMNS WHERE TABLE_SCHEMA='youdingyue';
```

##### 修改列字符集

```
ALTER TABLE table_name CHANGE column_name VARCHAR(150) CHARACTER SET
utf8mb4 COLLATE utf8mb4_unicode_ci;
```
（👆上面这一步我实际上木有用到，因为之前建 schema 的时候没有对列进行字符集的设置，所以修改完表字符集就可以了~~）

#### 问题补充：

在修改表字符集的时候，其实碰到另一个问题。报错信息如下：

```
mysql> ALTER TABLE jobs CONVERT TO CHARACTER SET utf8mb4 COLLATE
utf8mb4_unicode_ci;
ERROR 1071 (42000): Specified key was too long; max key length is 767
bytes
```
报错原因在于：MySQL 索引长度的限制为767字节，原来使用的 utf8 编码最多占3个字节，对于 varchar(255) 而言，乘以3之后为765，小于767，而转为 utf8mb4 编码，一个字符最多可以占用4个字节，乘以4之后就会超出索引长度限制了。

因此，字符集修改为 utf8mb4 之后，还需要处理 MySQL index 767 字节的限制问题。

之前的 jobid 是 varchar(255), 需要修改字段长度。如下：

```
mysql> ALTER TABLE jobs MODIFY COLUMN jobid varchar(150) NOT NULL;
```

END...🐸

#### 参考资料

[Using Option Files]

[Overview of MySQL Programs]

[mysql/Java服务端对emoji的支持]

[使MySQL能够存储emoji表情字符的设置教程]


[Using Option Files]: https://dev.mysql.com/doc/refman/5.7/en/option-files.html
[Overview of MySQL Programs]: https://dev.mysql.com/doc/refman/5.5/en/programs-overview.html
[mysql/Java服务端对emoji的支持]: https://segmentfault.com/a/1190000000616820
[使MySQL能够存储emoji表情字符的设置教程]: http://www.jb51.net/article/76878.htm
[邮订阅]: https://youdingyue.luckykaiyi.com