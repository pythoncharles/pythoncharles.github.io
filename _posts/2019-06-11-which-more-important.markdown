---
layout: post
category: read
title:  "海量数据的解决方案！"
tags: [阅读,网络]
date: 2019-06-11 18:05:01
---
&emsp;&emsp;系统的架构无奈的选择的就是最简单的方式：一台应用服务器、一台数据库服务器、一台文件系统服务器，没有用到高级的技术，也没有用到分布式部署的方案。
下边整理的是一些针对海量数据和高并发情况下的解决方案，技术水平有限，欢迎留言指导。
<!-- more -->
```
1.使用缓存。
2.页面静态化技术。
3.数据库优化。
4.分类数据库中活跃的数据。
5.批量读取和延迟修改。
6.读写分离。
7.适应nosql和Hadoop技术。
8.分布式部署数据库。
9.应用服务和数据服务分离。
10.使用搜索引擎搜索数据库中的数据。
11.进行业务拆分。  
```
### 1.使用缓存
    使用缓存的方式可以通过程序代码将数据直接保存到内存中，
    例如通过使用Map或者ConcurrentHashMap；
    另一种，就是使用缓存框架：Redis、Ehcache、Memcache等。
    使用缓存框架的时候，我们需要关心的就是什么时候创建缓存和缓存失效策略
### 2.页面静态化技术。
    1.尽量减少HTTP请求。
    2.使用cdn(Content Delivery Network，即内容分发网络)。
    3.添加expire头，控制缓存的失效日期。
    4.采用Gzip压缩组件。
    5.将样式表放在头部。
    6.将脚本放在底部。
    7.避免使用css表达式。
    8.使用外部的JavaScript和css。
    9.减少DNS查询。
    10.精简JavaScript。
    11.避免重定向。
    12.使用ajax可以缓存。
### 3.数据库优化。
    1.数据库表结构优化。
        1.1、命名规范
            1.库名、表名、字段名必须使用小写字母，并采用下划线分割。

            2、库名、表名、字段名禁止超过32个字符
                库名、表名、字段名支持最多64个字符，但为了统一规范、易于辨识以及减少传输量，禁止超过32个字符。

            3、使用INNODB引擎。
                INNODB引擎是MySQL5.5版本以后的默认引擘，支持事务、行级锁，有更好的数据恢复能力、更好的并发性能，同时对多核、大内存、SSD等硬件支持更好，支持数据热备份等，因此INNODB相比MyISAM有明显优势。

                1.InnoDB支持事务，MyISAM不支持。
                2.InnoDB支持行级锁，MyISAM支持表级锁。
                3.InnoDB支持MVCC，MyISAM不支持。（MVCC (Multiversion Concurrency Control)，即多版本并发控制技术,它使得大部分支持行锁的事务引擎，不再单纯的使用行锁来进行数据库的并发控制，取而代之的是把数据库的行锁与行的多个版本结合起来，只需要很小的开销,就可以实现非锁定读，从而大大提高数据库系统的并发性能。 ）
                4.InnoDB支持外键，而MyISAM不支持 。
                5.InnoDB不支持全文索引，而MyISAM支持。
                    innodb引擎的4大特性 ：
                        1.插入缓冲（insert buffer)。
                        2.二次写(double write)。
                        3.自适应哈希索引(ahi)。
                        4.预读(read ahead)

            4、库名、表名、字段名禁止使用MySQL保留字。
                当库名、表名、字段名等属性含有保留字时，SQL语句必须用反引号引用属性名称，这将使得SQL语句书写、SHELL脚本中变量的转义等变得⾮非常复杂。

            5、禁止使用分区表。
                分区表对分区键有严格要求；分区表在表变大后，执⾏行DDL、SHARDING、单表恢复等都变得更加困难。因此禁止使用分区表，并建议业务端手动SHARDING。

            6.建议使用UNSIGNED存储非负数值。
                同样的字节数，非负存储的数值范围更大。如TINYINT有符号为 -128-127，无符号为0-255。

            7.建议使用INT UNSIGNED存储IPV4
                用UNSINGED INT存储IP地址占用4字节，CHAR(15)则占用15字节。另外，计算机处理整数类型比字符串类型快。
                使用INT UNSIGNED而不是CHAR(15)来存储IPV4地址，通过MySQL函数inet_ntoa和inet_aton来进行转化。
                IPv6地址目前没有转化函数，需要使用DECIMAL或两个BIGINT来存储。

            8.强烈建议使用TINYINT来代替ENUM类型。
                ENUM类型在需要修改或增加枚举值时，需要在线DDL，成本较高；ENUM列值如果含有数字类型，可能会引起默认值混淆。

            9.使用VARBINARY存储大小写敏感的变长字符串或二进制内容。
                VARBINARY默认区分大小写，没有字符集概念，速度快。

            10.INT类型固定占用4字节存储
                例如INT(4)仅代表显示字符宽度为4位，不代表存储长度。数值类型括号后面的数字只是表示宽度而跟存储范围没有关系，比如INT(3)默认显示3位，空格补齐，超出时正常显示，python、java客户端等不具备这个功能。

            11.区分使用DATETIME和TIMESTAMP。
                存储年使用YEAR类型。存储日期使用DATE类型。 存储时间(精确到秒)建议使用TIMESTAMP类型。
                DATETIME和TIMESTAMP都是精确到秒，优先选择TIMESTAMP，因为TIMESTAMP只有4个字节，而DATETIME8个字节。
                同时TIMESTAMP具有自动赋值以及⾃自动更新的特性。注意：在5.5和之前的版本中，如果一个表中有多个timestamp列，那么最多只能有一列能具有自动更新功能。

            12.所有字段均定义为NOT NULL。
                对表的每一行，每个为NULL的列都需要额外的空间来标识。
                B树索引时不会存储NULL值，所以如果索引字段可以为NULL，索引效率会下降。
                建议用0、特殊值或空串代替NULL值。

    2.SQL语句优化。
        1、当只要一行数据时使用LIMIT 1
        2、为搜索字段建索引
        3、在Join表的时候使用相当类型的列，并将其索引
        4、千万不要ORDER BY RAND()
        5、SELECT只获取必要的字段、避免SELECT *
        6、用IN代替OR。SQL语句中IN包含的值不应过多，应少于1000个。
        7、SQL中避免出现now()、rand()、sysdate()、current_user()等不确定结果的函数。
        8、避免使用存储过程、触发器、视图、自定义函数等。（这些高级特性有性能问题，以及未知BUG较多。业务逻辑放到数据库会造成数据库的DDL、SCALE OUT、SHARDING等变得更加困难。）
        9、不要在MySQL数据库中存放业务逻辑。

    3.分区。
    4.分表。
    5.索引优化。
    MySQL的优化主要分为结构优化（Scheme optimization）和查询优化（Query optimization）。
            3.2、索引优化策略
                .最左前缀匹配原则
                .主键外检一定要建索引
                .对 where,on,group by,order by 中出现的列使用索引
                .尽量选择区分度高的列作为索引,区分度的公式是count(distinct col)/count(*)，表示字段不重复的比例，比例越大我们扫描的记录数越少，唯一键的区分度是1，而一些状态、性别字段可能在大数据面前区分度就是0
                .对较小的数据列使用索引,这样会使索引文件更小,同时内存中也可以装载更多的索引键
                .索引列不能参与计算，保持列“干净”，比如from_unixtime(create_time) = ’2014-05-29’就不能使用到索引，原因很简单，b+树中存的都是数据表中的字段值，但进行检索时，
                 需要把所有元素都应用函数才能比较，显然成本太大。所以语句应该写成create_time = unix_timestamp(’2014-05-29’);
                .为较长的字符串使用前缀索引
                .尽量的扩展索引，不要新建索引。比如表中已经有a的索引，现在要加(a,b)的索引，那么只需要修改原来的索引即可
                .不要过多创建索引, 权衡索引个数与DML之间关系，DML也就是插入、删除数据操作。这里需要权衡一个问题，建立索引的目的是为了提高查询效率的，但建立的索引过多，会影响插入、删除数据的速度，
                 因为我们修改的表数据，索引也需要进行调整重建
                .对于like查询，”%”不要放在前面。
                .查询where条件数据类型不匹配也无法使用索引 ，字符串与数字比较不使用索引;
    6.使用存储过程代替直接操作。

### 4.分类数据库中活跃的数据。
### 5.批量读取和延迟修改。
### 6.读写分离。
### 7.适应nosql和Hadoop技术。
### 8.分布式部署数据库。
### 9.应用服务和数据服务分离。
### 10.使用搜索引擎搜索数据库中的数据。
### 11.进行业务拆分。

#### 一.高并发情况下的解决方案
+ 1.应用程序和静态资源文件进行分离
        ```
        所谓的静态资源就是我们网站中用到的Html、Css、Js、Image、Video、Gif等静态资源。应用程序和静态资源文件进行分离也是常见的前后端分离的解决方案，
        应用服务只提供相应的数据服务，静态资源部署在指定的服务器上（Nginx服务器或者是CDN服务器上），前端界面通过Angular JS或者Node JS提供的路由技术访问应用服务器的具体服务获取相应的数据在前端游览器上进行渲染。
        这样可以在很大程度上减轻后端服务器的压力。例如，百度主页使用的图片就是单独的一个域名服务器上进行部署的
        ```
+ 2.页面缓存
        ```
        页面缓存是将应用生成的很少发生数据变化的页面缓存起来，这样就不需要每次都重新生成页面了，
        从而节省大量CPU资源，如果将缓存的页面放到内存中速度就更快。
        可以使用Nginx提供的缓存功能，或者可以使用专门的页面缓存服务器Squid。
        ```
+ 3.集群与分布式
+ 4.反向代理
+ 5.CDN
