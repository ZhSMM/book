# MySQL必知必会
---

- [基本数据类型](#基本数据类型)
- [整型类型](#整型类型)
- [浮点类型](#浮点类型)
- [简单查询](#简单查询)
- [子查询](#子查询)
- [临时表](#临时表)
- [视图](#视图)
- [存储过程执行动态SQL](#存储过程执行动态sql)


---


##### 基本数据类型

##### 整型类型

MySQL 数据库支持SQL标准支持的整型类型：INT、SMALLINT

| 类型      | 占用空间 | 取值范围(signed) | 取值范围(unsigned) |
| --------- | -------- | ---------------- | ------------------ |
| tinyint   | 1        | -128~127         | 0~255              |
| smallint  | 2        | -32768~32767     | 0~65535            |
| mediumint | 3        | -2^23^~2^23^-1-  | 0~2^24^-1          |
| int       | 4        | -2^31^~2^31^-1   | 0~2^32^-1          |
| bigint    | 8        | -2^63^~2^63^-1   | 0~2^64^-1          |

> 注意事项：unsigned表示不存负数，如果unsigned类型字段参与运算，得到的结果为负数，sql会报错；在SQL_MODE中加入NO_UNSIGNED_SUBTRACTION后，运算结果可以为负数。
>
> ```sql
> # 创建无符号类型表
> CREATE TABLE `sales` (
>   `id` int unsigned NOT NULL AUTO_INCREMENT,
>   `name` varchar(255) DEFAULT NULL,
>   `account` bigint unsigned DEFAULT NULL,
>   PRIMARY KEY (`id`)
> )
> 
> # 插入数据
> INSERT INTO `test_db`.`sales` (`id`, `name`, `account`) VALUES 
> (1, 'Sato Ayano', 806), 
> (2, 'Chiang Suk Yee', 717);
> 
> # 查询：报错BIGINT UNSIGNED value is out of range in '(`test_db`.`s2`.`account` - `test_db`.`s1`.`account`)'
> SELECT s1.id, s2.account - s1.account AS diff_count FROM sales s1 LEFT JOIN sales s2 ON s1.id = s2.id + 1;
> 
> # 设置sql_mode后执行成功
> SET sql_mode='NO_UNSIGNED_SUBTRACTION';
> 
> # 取消设置
> SET sql_mode='';
> ```



##### 浮点类型



##### 简单查询

基本查询语句语法：

```sql
# 基本查询
SELECT 列名, 列名, ...
FROM 表名
WHERE 列名 = 条件;

# 查找空值：is null和is not null
SELECT * FROM tbl_name WHERE name is null;

# 重命名列名：as
SELECT name as '用户名', age as '年龄' FROM tbl_name where age = 18;

# 去掉重复数据：distinct
# distinct语法规定，在对单字段、多字段去重时，必须放在第一个查询字段前
SELECT distinct name, age FROM tbl_name;

# 
```





##### 子查询



##### 临时表

with…as语句可以将SQL语句中的子查询定义为临时表，起到提高SQL语句可读性的作用，因此它也被归为子查询部分。一个with…as语句中可以定义多个临时表，多个临时表用逗号分隔；使用临时表时，可以用select语句查询临时表中的数据。

```sql
with 
    临时表名称1 as 子查询语句1,
    临时表名称2 as 子查询语句2,
...
```







##### 视图

创建视图的语法结构：

```sql
# 创建视图
create/replace view <view_name> [(字段列表)] AS <查询语句>
```



##### 存储过程执行动态SQL

在存储过程中动态执行SQL语句：MySQL5.0以后，支持动态SQL语句，当SQL语句中字段名、表名、数据库名等需要作为变量时，必须要使用动态SQL。基本使用语法如下：

1. 定义要执行的SQL变量，并赋值
2. 预定义好要执行的SQL
3. 执行预定义的SQL
4. 释放数据库连接

```sql
# 删除存储过程
drop procedure if exists test_name;

# 定义存储过程
delimiter //
create procedure test_name(in num1 int, in num2 int)
begin
  -- SELECT占位符
  set @sql = 'select ? + ?';
  set @param1 = num1;
  set @param2 = num2;
  PREPARE stmt from @sql;
  EXECUTE stmt USING @param1, @param2;
  DEALLOCATE PREPARE stmt;
  
  -- order by、limit使用占位符
  set @a = 'name';
  set @b = 10;
  set @c = 10;
  set @v_sql = "select * from student s ORDER BY ? LIMIT ?,?";
  PREPARE stmt from @v_sql;
  EXECUTE stmt using @a, @b, @c;
  DEALLOCATE PREPARE stmt;
end //
delimiter ;

# 调用存储过程
call test_name(10, 12);
```

MySQL 在存储过程中不支持直接使用变量名作为表名或列名，可以使用CONCAT拼接变量实现：

```sql
# 删除原有定义
drop procedure if exists test_name;

# 定义存储过程
delimiter //
create procedure test_name(in table_name varchar(50), in column_name varchar(50))
begin
  -- 删除临时表
  set @del_table = concat('drop table if exists ', table_name);
  prepare stmt from @del_table;
  execute stmt;
  deallocate prepare stmt;
  
  -- 创建临时表
  set @create_table = concat('create temporary table if not exists ', table_name, 
                             '(', column_name, ' varchar(50), id int(11), name varchar(50));');
  prepare stmt from @create_table;
  execute stmt;
  deallocate prepare stmt;
  
  -- 描述表结构
  set @desc_table = concat('desc ', table_name);
  prepare stmt from @desc_table;
  execute stmt;
  deallocate prepare stmt;
end //
delimiter ;

# 调用存储过程
call test_name('test_table', 'dynamic_column');
```

