# 		数据库MySQL



![微信图片_20231021104617](C:\Users\Aba1\Desktop\微信图片_20231021104617.png)



## MySQL体系结构介绍

------

### MySQL体系结构总览

Mysql是由SQL接口，解析器，优化器，缓存，存储引擎组成的。

第一层：Connection Pool

第二层：SQL Interface、Parser、Optimizer、Caches&Buffers

第三层：Pluggable Storage Engines

如下图：

![屏幕截图 2023-10-22 120428](C:\Users\Aba1\Desktop\屏幕截图 2023-10-22 120428.png)

1. Connectors指的是不同语言中与SQL的交互
2. Management Serveices & Utilities：系统管理和控制工具，例如备份恢复、Mysql复制、集群等
3. Connection Pool: 连接池：管理缓冲用户连接、用户名、密码、权限校验、线程处理等需要缓存的需求
4. 4SQL Interface: SQL接口：接受用户的SQL命令，并且返回用户需要查询的结果。比如select from就是调用SQL Interface
5. Parser: 解析器，SQL命令传递到解析器的时候会被解析器验证和解析。解析器是由Lex和YACC实现的，是一个很长的脚本，主要功能：将SQL语句分解成数据结构，并将这个结构传递到后续步骤，以后SQL语句的传递和处理就是基于这个结构的如果在分解构成中遇到错误，那么就说明这个sql语句是不合理的
6. Optimizer: 查询优化器，SQL语句在查询之前会使用查询优化器对查询进行优化。它使用的是“选取-投影-联接”策略进行查询。
7. Cache&Buffer（高速缓存区）：查询缓存，如果查询缓存有命中的查询结果，查询语句就可以直接去查询缓存中取数据。
8. Engine：存储引擎。存储引擎是MySql中具体的与文件打交道的子系统。也是Mysql最具有特色的一个地方。

```tex
Mysql的存储引擎是插件式的。它根据MySql AB公司提供的文件访问层的一个抽象接口来定制一种文件访问机制（这种访问机制就叫存储引擎）现在有很多种存储引擎，各个存储引擎的优势各不一样，最常用的MyISAM,InnoDB,BDB默认下MySql是使用MyISAM引擎，它查询速度快，有较好的索引优化和数据压缩技术。但是它不支持事务。InnoDB支持事务，并且提供行级的锁定，应用也相当广泛。Mysql也支持自己定制存储引擎，甚至一个库中不同的表使用不同的存储引擎，这些都是允许的
```

### 根据结构体系定位问题

1.连接层：最上层是客户端和连接服务，包含本地sock通信和大多数基于C/S 实现的类似tcp/ip通信。在该层引入线程池的概念，为通过认证安全接入的客户端提供线程。

  ---- 大部分C/S架构：客户端需要通过各种连接协议，才能沟通到服务端。此处，可利用JDBC方式连接处理授权安全。

2.服务层：第二层架构主要完成核心服务功能。在该层，服务器会解析查询并创建相应的内部解析树，并对其完成相应优化，如确定查询表的顺序，是否利用索引等，最后生成执行操作。若是select语句，服务器还会查询内部缓存。如果缓存空间够大。在数据量大的环境中能提升系统性能。

3.引擎层第三层存储引擎层，真正负责MySQL数据的存储和提取，服务器通过API与存储引擎进行通信，不同存储具有功能不同，这样可根据实际需求进行选取。

4.存储层第四层数据存储层，主要讲数据存储在裸设备的文件系统上，并完成与存储引擎的交互。



==数据库特性==

**ACID**

1.原子性

2.一致性

3.持久性

4.隔离性

## MySQL储存引擎

------

数据库集群，引擎搭建，环境要求（整体(服务器要求、版本要求一致、)化一，引擎一致、）

### 常用的存储引擎介绍

#### **MyISAM**

使用这个存储引擎，每个MyISAM在磁盘上存储成三个文件。

（1）frm文件：存储表的定义数据

（2）MYD文件：存放表具体记录的数据

（3）MYI文件：存储索引

MyISAM支持数据的类型也有三种：

（1）静态固定长度表

这种方式的优点在于存储速度非常快，容易发生缓存，而且表发生损坏后也容易修复。缺点是占空间。这也是默认的存储格式。

（2）动态可变长表

优点是节省空间，但是一旦出错恢复起来比较麻烦。

（3）压缩表

上面说到支持数据压缩，说明肯定也支持这个格式。在数据文件发生错误时候，可以使用check table工具来检查，而且还可以使用repair table工具来恢复。

==有一个重要的特点那就是不支持事务，但是这也意味着他的存储速度更快，如果你的读写操作允许有错误数据的话，只是追求速度，可以选择这个存储引擎。==

#### **INNODB**

InnoDB是默认的数据库存储引擎，他的主要特点有：

（1）可以通过自动增长列，方法是auto_increment。

（2）支持事务。默认的事务隔离级别为可重复度，通过MVCC（并发版本控制）来实现的。

（3）使用的锁粒度为行级锁，可以支持更高的并发；

（4）支持外键约束；外键约束其实降低了表的查询速度，但是增加了表之间的耦合度。

（5）配合一些热备工具可以支持在线热备份；

（6）在InnoDB中存在着缓冲管理，通过缓冲池，将索引和数据全部缓存起来，加快查询的速度；

（7）对于InnoDB类型的表，其数据的物理组织形式是聚簇表。所有的数据按照主键来组织。数据和索引放在一块，都位于B+数的叶子节点上；

当然InnoDB的存储表和索引也有下面两种形式：

（1）使用共享表空间存储：所有的表和索引存放在同一个表空间中。

（2）使用多表空间存储：表结构放在frm文件，数据和索引放在IBD文件中。分区表的话，每个分区对应单独的IBD文件，分区表的定义可以查看我的其他文章。使用分区表的好处在于提升查询效率。

==对于InnoDB来说，最大的特点在于支持事务。但是这是以损失效率来换取的。==

#### **两者比较总结**

MYISAM不支持事务、外键约束,采用表级锁,数据直接存文件,更适合读密集的应用场景。

INNODB支持事务、外键约束,采用行级锁,有自身的缓冲池,更适合对事务完整性有较高要求的应用场景。

两者在事务性、锁机制、缓存策略上有显著区别,INNODB的特性更适合对数据一致性和完整性要求较高的应用,而MYISAM的简单机制则更适合读密集型应用

```mysql
-- InnoDB 是系统的默认引擎，支持可靠的事务处理。使用下面的语句可以修改数据库临时的默认存储引擎
SET default_storage_engine=< 存储引擎名 >
```



## SQL分类

------

### DDL

#### **库的CRUD操作**

##### **查看数据库**

```mysql
show databases;#查看所有数据库
show create database 数据库名称;#查看数据库定义
select database();#查看当前正在使用数据库的名字
```

##### **库的创建**

```mysql
create database [if not exists] 数据库的名字 [default charset 字符集]
```

##### **修改数据库**

```mysql
#重命名（已废弃）
RENAME DATABASE 旧库名 TO 新库名;
#修改库的字符集 utf8mb4
ALTER DATABASE 库名 CHARSET SET 字符集名;
```

##### **删除数据库名**

```mysql
DROP DATABASE [IF EXISTS] 数据库名;
```

##### **选中数据库**

```mysql
use 数据库名;
```



#### **表的CRUD操作**

```mysql
-- 当前库底下的表
show TABLES;
-- 查看表结构
desc account;
-- 查看表创建结构：引擎是ENGINE=InnoDB
SHOW CREATE TABLE account;
-- 查看当前数据库引擎
SHOW ENGINES;
```



##### 表的创建

```mysql
create table 表名(
字段 字段1类型 [comment'字段1注释'],
字段 字段2类型 [comment'字段2注释'],
......
字段 字段n类型 [comment'字段n注释']
)ENGINE=INNODB [comment'表注释']
```



##### 表的修改

 ```mysql
 # 添加列
 ALTER TABLE 表名 ADD COLUMN 列名 类型 FIRST;# 添加到首列
 ALTER TABLE 表名 ADD COLUMN 列名 类型 AFTER 字段名;# 添加在某列之后
 
 #修改列的类型或约束
 ALTER TABLE 表名 MODIFY COLUMN 列名 新类型 [新约束];
 
 #修改列名
 ALTER TABLE 表名 CHANGE COLUMN 旧列名 新列名 类型;
 
 #删除列
 ALTER TABLE 表名 DROP COLUMN 列名;
 
 #修改表名
 ALTER TABLE 表名 RENAME [TO] 新表名;
 ```



##### 表的删除

```mysql
#方式一
DROP TABLE [IF EXISTS] 表名;
#用于删除表的结构，属性，索引
#方式二
TRUNCATE TABLE [IF EXISTS] 表名;
#清空表里的所有数据，但保留表
```



#### **常见约束**

含义：

约束是一种限制，用于限制表中的数据，为了保证数据库中表里的数据的准确性和可靠性

分类：

| 约束        | 意义                                             |
| ----------- | ------------------------------------------------ |
| NOT NULL    | 非空，该字段的值必填                             |
| UNIQUE      | 唯一，该字段的值不可重复                         |
| DEFAULT     | 默认，该字段中不插入值也会输入默认值             |
| CHECK       | 检查，MySQL(8.0.16版本之后)支持                  |
| PRIMARY KEY | 主键，该字段的值不可重复且非空-->UNIQUE+NOT NULL |
| FOREIGN KEY | 外键，该字段的值引用了另外的表的字段             |

特点：

主键和唯一

```mysql
#不同点
1.一个表至多只有一个主键，但可以有多个唯一
2.主键不允许为空，但唯一可以

#相同点
1.都具有唯一性
2.都支持组合键，但不推荐
```

外键

```mysql
1.用于限制两个表之间的关系，从表的字段里引用了主表里字段的值，保证了数据的一致性和完整性
2.外键列和主表的被引用列要求类型一致，意义一样，名称无要求。
3.主表的被引用列要求是一个key，一般就是主键
4.插入数据应当先插入主表，删除数据应当先删除子表
```



#### 自增长列

含义：

自增长列又称表示列，他可以不用手动插入值，系统提供默认的序列值

特点：

1.不用手动插入值，可以提供序列值，默认从一开始，步长为1，如果要更改起始值，第一次手动插入值，后续使用NULL，如果要更改步长，修改系统变量：SET AUTO_INCREMENT_INCREMENT=值;

2.一个表至多只有一个自增长列

3.增长列只能支持数值型

4.自增长必须为一个KEY

**演示**

创建表时添加自增长列

```mysql
CREATE TABLE 表名 （
字段名 类型 约束 AUTO_INCREMENT
）;
```



修改表时添加或删除自增长列

```mysql
#添加自增长列
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 约束 AUTO_INCREMENT;

#删除自增长列
ALTER TABLE 表名 MODIFY COLUMN 字段名 字段类型 约束;
```



### SQL



## 锁

------

### 概述

锁是计算机协调多个进程或线程并发访问某一资源的机制。在数据库中，除传统的计算资源（CPU、RAM、I/O）的争用以外，数据也是一种供许多用户共享的资源。如何保证数据并发访问的一致性、有效性是所有数据库必须解决的一个问题，锁冲突也是影响数据库并发访问性能的一个重要因素。从这个角度来说，锁对数据库而言显得尤其重要，也更加复杂。

### 锁的力度分配

全局锁：锁定数据库中的所有表。

表级锁：每次操作锁住整张表。

行级锁：每次操作锁住对应的行数据

### 全局锁

全局锁就是对整个数据库实例加锁，加锁后整个实例就处于只读状态，后续的DML的写语句，DDL语句，已经更新操作的事务提交语句都将被阻塞。其典型的使用场景是做全库的逻辑备份，对所有的表进行锁定，从而获取一致性视图，保证数据的完整性。

**处理方法**

对数据库进行进行逻辑备份之前，先对整个数据库加上全局锁，一旦加了全局锁之后，其他的DDL、DML全部都处于阻塞状态，但是可以执行DQL语句，也就是处于只读状态，而数据备份就是查询操作。那么数据在进行逻辑备份的过程中，数据库中的数据就是不会发生变化的，这样就保证了数据的一致性和完整性

#### **语法**

```mysql
-- 加全局锁
flush tables with with read lock;
-- 数据备份：
mysqldump -uroot -proot 数据库名 > 存放地址+数据库名.sql
-- 释放锁
unlock tables;
```
```mysql
-- 窗口1:
-- 加全局锁
flush tables with read lock;
-- 窗口2:
-- 查询和更新语句
SELECT * from student update student setno="黄嘉怡"WHERE id=1;
-- 出现阻塞
-- 运行cmd-- 数据备份：
mysqldump -uroot -proot 数据库名> 存放地址+数据库名.sqlmysqldump -uroot -proot javase2301>D:/java2301.sql
-- 窗口1：
-- 释放锁
unlocktables
-- 窗口2：执行更新语句
update student setno="黄嘉怡"WHERE id=1;
-- 更新成功
```

#### **特点**

数据库中加全局锁，是一个比较重的操作，存在以下问题：如果在主库上备份，那么在备份期间都不能执行更新，业务基本上就得停摆。如果在从库上备份，那么在备份期间从库不能执行主库同步过来的二进制日志（binlog），会导致主从延迟。在InnoDB引擎中，我们可以在备份时加上参数 --single-transaction 参数来完成不加锁的一致性数据备份

```mysql
-- 运行cmd输入
mysqldump --single-transaction -uroot -proot javase2301 >D:/java2302.sql
-- --single-transactio底层实现的是快照
```

### 表锁

#### 分类

+ 表共享读锁（read lock）
+ 表独占写锁（write lock）

#### 语法

+ 加锁：lock tables 表名... read/write。
+ 释放锁：unlock tables / 客户端断开连接。

#### 特点

读锁：对指定表加了读锁，不会影响其他客户端读，但会阻塞其他客户端写

```mysql
-- 窗口1：
-- 加锁
use javase2301 lock tables student read ;update student set name="黄1"WHERE id=1;
-- 失败
-- 窗口2：
select * from student;update student set name="黄1"WHERE id=1; 
-- 阻塞
-- 窗口1
-- 释放锁
UNLOCK tables
-- 窗口2
马上更新
```

写锁：对指定表加了写锁，不但会影响其他客户端读，还会阻塞其他客户端写

```mysql
-- 窗口1：
-- 加锁
use javase2301 lock tables student write ;
update student set studentname="黄1"WHEREid=1;
-- 窗口2：
select * from student; 
-- 阻塞
-- 窗口1
-- 释放锁
UNLOCK tables;
-- 窗口2
马上查询
-- ---------------------- -----------------------------------------
-- 窗口1
use javase2301 lock tables student write ;
-- 窗口2
update student set studentname="黄1"WHEREid=1;
-- 阻塞
-- 窗口1
-- 释放锁
UNLOCK tables
-- 窗口2
马上更新
```

### 行锁

行级锁，每次操作锁住对应的行数据。锁定粒度最小，发生锁冲突的概率最低，并发度最高。应用在InnoDB存储引擎中。InnoDB的数据是基于索引组织的，行锁是通过对索引上的索引项加锁来实现的，而不是对记录加的锁行锁：锁定单个行记录的锁，防止其他事务对此行进行update和delete。在RC、RR隔离级别下都支持。

#### 分类

InnoDB实现了以下两种类型的行锁：

+ 共享锁（S）：允许一个事务去读一行，阻止其他事务获得相同数据集的排它锁。
+ 排他锁（X）：允许获取排他锁的事务更新数据，阻止其他事务获得相同数据集的共享锁和排他锁



| 当前锁类型  | 共享锁（S） | 排他锁（X） |
| :---------: | :---------: | :---------: |
| 请求锁类型  |             |             |
| 共享锁（S） |    兼容     |    冲突     |
| 排他锁（X） |    冲突     |    冲突     |



#### SQL行锁

| SQL                                                 | 类型        | 说明                             |
| --------------------------------------------------- | ----------- | -------------------------------- |
| INSERT                                              | 排他锁（X） | 自动加锁                         |
| UPDATE                                              | 排他锁（X） | 自动加锁                         |
| DELETE                                              | 排他锁（X） | 自动加锁                         |
| SELECT(正常)                                        | 不加锁      |                                  |
| SELECT ... LOCK IN SHARE MODE                       | 共享锁（S） | 手动加                           |
| SELECT之后加LOCK IN SHARE MODE SELECT ...FOR UPDATE | 排他锁（X） | 需要手动在SELECT之后加FOR UPDATE |

+ 默认情况下，InnoDB在 REPEATABLE READ事务隔离级别运行，InnoDB使用 next-key 锁进行搜索和索引扫描，以防止幻读。

+ 针对唯一索引进行检索时，对已存在的记录进行等值匹配时，将会自动优化为行锁。

+ InnoDB的行锁是针对于索引加的锁，==不通过索引条件检索数据，那么InnoDB将对表中的所有记录加锁，此时就会升级为表锁==

#### 语法

 ```mysql
 -- 查看行锁加锁情况
 select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;
 ```



SELECT语句正常不加锁

```mysql
-- 窗口1：
SELECT * FROM student where id=1;
-- 窗口2：查询加锁情况
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data fromperformance_schema.data_locks;
SELECT * FROM student where id=1; 
-- 无加锁可以执行
```

共享锁

```mysql
-- 窗口1加共享锁
START TRANSACTION;
SELECT * FROM student where id=1 lock in share mode
-- 窗口2查询加锁情况
START TRANSACTION;
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;
--  有一个共享锁
-- 窗口2添加共享锁测试共享锁兼容
SELECT * FROM student where id=1 lock in share mode 
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks; 
-- 窗口1共享锁窗口2共享锁
-- 窗口2
COMMIT
-- 行锁释放了
selectobject_schema,object_name,index_name,lock_type,lock_mode,lock_data fromperformance_schema.data_locks; -- 行锁释放一个
-- 窗口1
COMMIT
-- 行锁释放了
```

共享锁与其他锁之间排斥

```mysql
-- 前提：id是相同的
-- 窗口1
START TRANSACTION;
SELECT * FROM student where id=1 lock in share mode;
-- 窗口2 排他锁
select object_schema,object_name,index_name,lock_type,lock_mode,lock_data from performance_schema.data_locks;
UPDATE student set studentname="黄嘉1" WHERE id=1;  
-- 阻塞
-- 结论：共享锁与排他锁之间互斥
-- 窗口1
commit
```

排他锁与排他锁之间互斥

```mysql
-- 前提：id是相同的
-- 窗口1 加上排他锁
START TRANSACTION;
UPDATE student set studentname="黄嘉1" WHERE id=1
-- 窗口2 加上排他锁
START TRANSACTION;
UPDATE student set studentname="黄嘉1" WHEREid=1
-- 阻塞
-- 只有当窗口1提交，释放排他锁，窗口2马上更新
-- 窗口1
commit
-- 窗口2
commit
```

 无索引行锁升级为表锁

```mysql
-- 窗口1
START TRANSACTION;
UPDATE student set studentname="黄嘉1" WHERE studentname="黄嘉2";
-- 窗口2 
START TRANSACTION;
UPDATE student set studentname="陈欣5" WHEREid=4;
-- 原因就是因为此时，客户端一，根据studentname字段进行更新时，studentname字段是没有索引的，如果没有索引，此时行锁会升级为表锁(因为行锁是对索引项加的锁，而studentname没有索引)。
-- 窗口1
COMMIT;
-- 窗口2
COMMIT; 
-- ---------------------给studentname添加索引--------------- 
-- 窗口1
drop index index_stu_name on student show index from student
-- 创建索引
CREATE index index_stu_name on student(studentname);
-- 窗口1 
START TRANSACTION;
UPDATE student set studentname="黄嘉2" WHERE studentname="黄嘉1";
-- 窗口2
START TRANSACTION;
UPDATE student set studentname="陈欣5" WHERE id=4
-- 更新成功
-- 窗口1
COMMIT;
-- 窗口2
COMMIT;
```

