# SQL Introduction
## data format
名称 | 类型 | 说明
:-: | :-: | :-:
INT | 整型 | 4字节整数类型，范围约+/-21亿
TINYINT | 整型 | 0~255
BIGINT | 长整型 | 8字节整数类型，范围约+/-922亿亿(**常用**)
REAL/FLOAT(24) | 浮点型 | 4字节浮点数，范围约+/-1038
DOUBLE | 浮点型 | 8字节浮点数，范围约+/-10308
DECIMAL(M,N) | 高精度小数 | 由用户指定精度的小数，例如，DECIMAL(20,10)表示一共20位，其中小数10位，通常用于财务计算
CHAR(N) | 定长字符串 | 存储指定长度的字符串，例如，CHAR(100)总是存储100个字符的字符串
VARCHAR(N) | 变长字符串 | 存储可变长度的字符串，例如，VARCHAR(100)可以存储0~100个字符的字符串(**常用**)
BOOLEAN | 布尔类型 | 存储True或者False
DATE | 日期类型 | 存储日期，例如，2018-06-22
TIME | 时间类型 | 存储时间，例如，12:20:59
DATETIME | 日期和时间类型 | 存储日期+时间，例如，2018-06-22 12:20:59
## SQL product
     1. 商用数据库，例如：Oracle，SQL Server，DB2等；
     2. 开源数据库，例如：MySQL，PostgreSQL等；
     3. 桌面数据库，以微软Access为代表，适合桌面应用程序使用；
     4. 嵌入式数据库，以Sqlite为代表，适合手机应用和桌面程序。
## 主键
1. 自增整数类型：数据库会在插入数据时自动为每一条记录分配一个自增整数，这样我们就完全不用担心主键重复，也不用自己预先生成主键；
2. 全局唯一**GUID**类型：使用一种全局唯一的字符串作为主键，类似**8f55d96b-8acc-4636-8cb8-76bf8abc2f57**。GUID算法通过网卡MAC地址、时间戳和随机数保证任意计算机在任意时间生成的字符串都是不同的，大部分编程语言都内置了GUID算法，可以自己预算出主键。

## 外键
```
-- add a foreign key
ALTER TABLE students
ADD CONSTRAINT fk_class_id
FOREIGN KEY (class_id)
REFERENCES classes (id);

-- delete a foreign key
ALTER TABLE students
DROP FOREIGN KEY fk_class_id;
```

外键约束的名称fk_class_id可以任意，FOREIGN KEY (class_id)指定了class_id作为外键，REFERENCES classes (id)指定了这个外键将关联到classes表的id列。

## index
```
-- create index
ALTER TABLE students
ADD INDEX idx_score (score);
-- create unique index
ALTER TABLE students
ADD UNIQUE INDEX uni_name (name);
-- create unique index for a col
ALTER TABLE students
ADD CONSTRAINT uni_name UNIQUE (name);
```
***
# MySQL installing
```
apt-get install mysql-server
sudo mysql_secure_installation
systemctl status mysql.service
```

# SQL Grammar
## query data
```
-- query all data from a table
SELECT * FROM table_name;
-- query data that meets conditions from a table
SELECT * FROM table_name WHERE <conditional expression>
SELECT * FROM table_name WHERE <conditional expression 1> AND <conditional expression 2> OR <conditional expression 3>
SELECT * FROM table_name WHERE NOT <conditional expression>
```
按照NOT、AND、OR的优先级进行，即NOT优先级最高，其次是AND，最后是OR。加上括号 " ( ) " 可以改变优先级。
### commonly conditional expression
条件 | 表达式举例1 | 表达式举例2 | 说明
:-: | :-: | :-: | :-:
使用=判断相等 | score = 80 | name = 'abc' | 字符串需要用单引号括起来
使用>判断大于 | score > 80 | name > 'abc' | 字符串比较根据ASCII码，中文字符比较根据数据库设置
使用>=判断大于或相等 | score >= 80 | name >= 'abc' | 
使用<判断小于 | score < 80 | name <= 'abc' | 
使用<=判断小于或相等 | score <= 80 | name <= 'abc' | 
使用<>判断不相等 | score <> 80 | name <> 'abc' | 
使用LIKE判断相似 | name LIKE 'ab%' | name LIKE '%bc%' | %表示任意字符，例如'ab%'将匹配'ab'，'abc'，'abcd'

```
-- query data(colName1, colName2, colName3, ...) from a table
SELECT colName1, colName2, colName3, ... , FROM students;
SELECT colName1, colName2 aliasName2, colName3, ... , FROM students;
```
## order data
```
-- order data from slow to high
SELECT colName1, ... FROM tableName ORDER BY colName1 (ASC);
-- order data from high to slow
SELECT colName1, ... FROM tableName ORDER BY colName1 DESC;
-- order data
SELECT colName1, ... FROM tableName ORDER BY colName1, colName2;

SELECT colName1, ... FROM tableName WHERE <conditional expression> ORDER BY colName1, colName2;
```
## Pagination
```
-- 
SELECT colName1, ... FROM tableName WHERE <conditional expression> ORDER BY colName1, colName2 LIMIT 3 OFFSET 0;
--
SELECT colName1, ... FROM tableName WHERE <conditional expression> ORDER BY colName1, colName2 LIMIT 3;
--
SELECT colName1, ... FROM tableName WHERE <conditional expression> ORDER BY colName1, colName2 LIMIT 0, 3;
--
SELECT colName1, ... FROM tableName WHERE <conditional expression> ORDER BY colName1, colName2 LIMIT pageSize OFFSET pageSize*(pageIndex-1);
```
## Aggregate query
```
-- count number of item
SELECT COUNT(*) FROM tableName;
SELECT COUNT(*) aliasName FROM tableName;
SELECT COUNT(*) aliasName FROM tableName WHERE <conditional expression>;
```

函数 | 说明 | no match conditional expression | example
COUNT | 计数 | return 0 | COUNT(*)
:-: | :-: | :-: | :-:
SUM | 计算某一列的合计值，**该列必须为数值类型** | return NULL | SUM(colName)
AVG | 计算某一列的平均值，**该列必须为数值类型** | return NULL | AVG(colName)
MAX | 计算某一列的最大值和排序最前的字符 | return NULL | MAX(colName)
MIN | 计算某一列的最小值和排序最后的字符 | return NULL | MIN(colName)
FLOOR | 向上取整 | 1 | FLOOR(COUNT(*))
CEILING | 向下取整 | 1 | CEILING(COUNT(*))


```
-- classifity group to queay data
SELECT colName, COUNT(*) aliasName FROM tableName GROUP BY colName;
--
SELECT colName1, colName2, COUNT(*) aliasName FROM tableName GROUP BY colName1, colName2;
```

## multi-table query

```
-- Cartesian query(笛卡尔查询)
SELECT * FROM tableName1, tableName2;

```
## Join query
```
SELECT t.colName1, t.colName2, t.colName3 FROM tableName t;

SELECT t1.colName1, t1.colName2, t1.colName3 FROM tableName1 t1 INNER JOIN tableName2 t2 ON t1.colName1 = t2.colName;
-- 
SELECT t1.colName1, t1.colName2, t1.colName3 FROM tableName1 t1 RIGHT OUTER JOIN tableName2 t2 ON t1.colName1 = t2.colName;
```
INNER JOIN只返回同时存在于两张表的行数据，由于students表的class_id包含1，2，3，classes表的id包含1，2，3，4，所以，INNER JOIN根据条件s.class_id = c.id返回的结果集仅包含1，2，3。

RIGHT OUTER JOIN返回右(**次表**)表都存在的行。如果某一行仅在右表存在，那么结果集就会以NULL填充剩下的字段。

LEFT OUTER JOIN则返回左表(**主表**)都存在的行。如果我们给students表增加一行，并添加class_id=5，由于classes表并不存在id=5的行，所以，LEFT OUTER JOIN的结果会增加一行，对应的class_name是NULL

FULL OUTER JOIN，它会把两张表的所有记录全部选择出来，并且，自动把对方不存在的列填充为NULL：












```
-- 如果test数据库不存在，就创建test数据库：
CREATE DATABASE IF NOT EXISTS test;

-- 切换到test数据库
USE test;

-- 删除classes表和students表（如果存在）：
DROP TABLE IF EXISTS classes;
DROP TABLE IF EXISTS students;

-- 创建classes表：
CREATE TABLE classes (
id BIGINT NOT NULL AUTO_INCREMENT,
name VARCHAR(100) NOT NULL,
PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 创建students表：
CREATE TABLE students (
id BIGINT NOT NULL AUTO_INCREMENT,
class_id BIGINT NOT NULL,
name VARCHAR(100) NOT NULL,
gender VARCHAR(1) NOT NULL,
score INT NOT NULL,
PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- 插入classes记录：
INSERT INTO classes(id, name) VALUES (1, '一班');
INSERT INTO classes(id, name) VALUES (2, '二班');
INSERT INTO classes(id, name) VALUES (3, '三班');
INSERT INTO classes(id, name) VALUES (4, '四班');

-- 插入students记录：
INSERT INTO students (id, class_id, name, gender, score) VALUES (1, 1, '小明', 'M', 90);
INSERT INTO students (id, class_id, name, gender, score) VALUES (2, 1, '小红', 'F', 95);
INSERT INTO students (id, class_id, name, gender, score) VALUES (3, 1, '小军', 'M', 88);
INSERT INTO students (id, class_id, name, gender, score) VALUES (4, 1, '小米', 'F', 73);
INSERT INTO students (id, class_id, name, gender, score) VALUES (5, 2, '小白', 'F', 81);
INSERT INTO students (id, class_id, name, gender, score) VALUES (6, 2, '小兵', 'M', 55);
INSERT INTO students (id, class_id, name, gender, score) VALUES (7, 2, '小林', 'M', 85);
INSERT INTO students (id, class_id, name, gender, score) VALUES (8, 3, '小新', 'F', 91);
INSERT INTO students (id, class_id, name, gender, score) VALUES (9, 3, '小王', 'M', 89);
INSERT INTO students (id, class_id, name, gender, score) VALUES (10, 3, '小丽', 'F', 85);

-- OK:
SELECT 'ok' as 'result:';
```




condition  [kənˈdɪʃn]  详细X
基本翻译
n. 条件；情况；环境；身份
vt. 决定；使适应；使健康；以…为条件
网络释义
Condition: 条件
health condition: 健康状况
standard condition: 标准状况
