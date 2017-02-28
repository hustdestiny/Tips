mysql 学习笔记

第一节
    登录          mysql -u root -p 密码直接enter

    显示数据库       show databases;
    显示警告        show warnings;

    创建数据库   CREATE DATABASE if not exists test CHARACTER SET uft8;
    显示参数        SHOW CREATE DATABASE test;
    修改数据库   ALTER DATABASE t1 CHARACTER SET = utf8
    删除数据库       DROP DATABASE if exists t1

第二节 
    1.数据类型
        整型
        TINYINT，SMALLINT，MEDIUMINT，INT，BIGINT

        浮点型
        FLOAT，DOUBLE

        日期
        TIMESTAMP，YEAR，TIME，DATE，DATETIME

        字符型
        CHAR(M)，VARCHAR(M)，TINYTEXT，TEXT，MEDIUMTEXT，LOGNTEXT，ENUM('value1','value2')，SET('value1','value2')

    2.数据表
        打开数据库use testbase; 
        显示目前打开的数据库select database();
        创建数据表 create table [if not exists] table_name(column_name data_type);
        显示数据表 show tables from database_name;
        显示表的column show columns from table_name
        插入记录 insert table_name (columns..) values(values..)
        查找记录 select (columns) from table_name
        非空 not null
        自动编号 autoincrement 必须与主键一起使用 insert table_name values (null|default)
        primary key 自动为not null，每张表只能有一个, 确保唯一性
        unique 可以为空，一张表可以有多个，确保唯一性
        default 如果没有赋值就是用默认值

第三节 约束
        列级约束, not null,default,
        表级约束, primary key, unique,  foreign key
        父表和子表必须使用InnoDB，禁止使用临时表，必须创建索引
        foreign key (column_name) references other_table (other_table_column)
        有外键的表称为子表，另一张表就是父表
        cascade 从父表中删除或者更新
        set null 从父表中删除或者更新时 设置null
        restrict 从父表中删除或者更新 拒绝更新
        no cation sql标准的关键字跟restrict一致

        添加列 
        alter table table_name add column_name column_definition [first | after exist_column]
        删除列
        alter table table_name drop column_name
        修改列

        增加主键
        alter table table_name add primary key (culumn_name)
        alter table table_name add unique (column_name)

        alter table table_name drop unique (column_name)

        删除约束
        show create table table_name;
        alter table table_name drop INDEXES username;
        show table indexes table_name;
        alter table table_name drop foreign key

        第四节 子查询

        使用比较运算符
        <,=,>
        any
        some
        all

        =any === in
        !=any === not in

        创建表 create table 
        insert select
        多表更新 

        create select

        连接
        join
        left join
        right join
        full join
        多表删除

第四章 自定义函数，自定义存储过程

第五章 存储引擎

第六章 客户端
mysqlworkbench

第七章 数据库设计

1.需求分析

        1.实体以及实体之前的关系（1对1，1对多，多对多）

        2.实体的属性有什么

        3.那些属性或者属性的组合可以唯一标识一个实体

2.逻辑设计

        1.将需求转化为数据库的逻辑模型

        2.通过ER图的形式对

        3.关系，元组，属性，候选码，主码，域，分量

        4.三范式

3.物理设计

        1.选择合适的数据库管理系统

        2.定义数据库，表以及字段的命名规范

        3.根据所选的DBMS系统选择合适的字段类型

        4.反范式化设计

        5.如果选择主键

        6.尽量避免使用外键

        7.避免使用触发器

        8.避免预留字段

        9.反范式化，空间换时间
