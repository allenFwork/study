# SQL 语句

## 一. SELECT 语句

### SELECT 简介

1. SELECT 语句用于从数据库中选取数据。

2. 结果被存储在一个结果表中，称为结果集。

### SQL SELECT 语法

```sql
SELECT column_name,column_name
FROM table_name;
```

与

```sql
SELECT * FROM table_name;
```



## <font color=#FF0000>二. SELECT DISTINCT 语句</font>

### SELECT DISTINCT 简介

1.  SELECT DISTINCT 语句用于返回唯一不同的值。 
2. 在表中，一个列可能会包含多个重复值，有时您也许希望仅仅列出不同（distinct）的值。
3. DISTINCT 关键词用于返回唯一不同的值。

### SQL SELECT 语法

```sql
SELECT DISTINCT column_name,column_name
FROM table_name;
```



## 三. WHERE 子句

### WHERE 子句  简介

1.  WHERE 子句用于过滤记录。
2.  WHERE 子句用于提取那些满足指定条件的记录。

### SQL SELECT 语法

```sql
SELECT column_name,column_name
FROM table_name
WHERE column_name operator value;
```

### WHERE 子句中的运算符

| 运算符  | 描述                                                       |
| :------ | :--------------------------------------------------------- |
| =       | 等于                                                       |
| <>      | 不等于。**注释：**在 SQL 的一些版本中，该操作符可被写成 != |
| >       | 大于                                                       |
| <       | 小于                                                       |
| >=      | 大于等于                                                   |
| <=      | 小于等于                                                   |
| BETWEEN | 在某个范围内                                               |
| LIKE    | 搜索某种模式                                               |
| IN      | 指定针对某个列的多个可能值                                 |



## 四. AND & OR 运算符

### AND & OR 运算符  简介

1. 如果第一个条件和第二个条件都成立，则 AND 运算符显示一条记录。
2. 如果第一个条件和第二个条件中只要有一个成立，则 OR 运算符显示一条记录。

### AND & OR 运算符 语法

```sql
SELECT column_name,column_name
FROM table_name
WHERE column_name operator value 
And column_name operator value;
```

与

```sql
SELECT column_name,column_name
FROM table_name
WHERE column_name operator value
OR column_name operator value;
```

### 结合 AND & OR

- 把 AND 和 OR 结合起来（使用圆括号来组成复杂的表达式）
- AND 的优先级高于 OR

```sql
SELECT column_name,column_name
FROM table_name
WHERE column_name operator value
And ( column_name operator value OR column_name operator value );
```



## 五. ORDER BY 关键字

### ORDER BY 关键字  简介

1.  ORDER BY 关键字用于对结果集按照一个列或者多个列进行排序。
2.  ORDER BY 关键字默认按照升序对记录进行排序。如果需要按照降序对记录进行排序，可以使用 DESC 关键字。

### ORDER BY 关键字 语法

```sql
SELECT column_name,column_name
FROM table_name
ORDER BY column_name,column_name ASC|DESC;
```



## 六. INSERT INTO 语句

### INSERT INTO 语句  简介

1.   INSERT INTO 语句用于向表中插入新记录 。

### INSERT INTO 语句 语法

1. 第一种形式无需指定要插入数据的列名，只需提供被插入的值即可：

```sql
INSERT INTO table_name
VALUES
(value1,value2,value3,...);
```

2. 第二种形式需要指定列名及被插入的值：

```sql
INSERT INTO table_name
(column1,column2,column3,...)
VALUES 
(value1,value2,value3,...);
```



## 七. UPDATE 语句

### UPDATE 语句  简介

1.  UPDATE 语句用于更新表中已存在的记录。

### UPDATE 语句 语法

```sql
UPDATE table_name
SET column1=value1,column2=value2,...
WHERE some_column=some_value;
```

**请注意 SQL UPDATE 语句中的 WHERE 子句！**

1. WHERE 子句规定哪条记录或者哪些记录需要更新。如果省略了 WHERE 子句，所有的记录都将被更新！ 

2. 在 MySQL 中可以通过设置 **sql_safe_updates** 这个自带的参数来解决，当该参数开启的情况下，你必须在update 语句后携带 where 条件，否则就会报错。

   **set sql_safe_updates=1;** 表示开启该参数



## 八. DELETE 语句

### DELETE 语句 简介

 DELETE 语句用于删除表中的行。 

### DELETE 语句 语法

```sql
DELETE FROM table_name
WHERE some_column = some_value;
```

**请注意 SQL DELETE 语句中的 WHERE 子句！**
WHERE 子句规定哪条记录或者哪些记录需要删除。如果省略了 WHERE 子句，所有的记录都将被删除！ 

### 删除所有数据

 可以在不删除表的情况下，删除表中所有的行:

```sql
DELETE FROM table_name;
```

或

```sql
DELETE * FROM table_name;
```

