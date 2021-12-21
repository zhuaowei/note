# MySQL 基本操作

## 前言

##### 什么是数据库

数据库就像一个大仓库，里面存了很多的数据，如果你要从中取出一点东西怎么办呢，一个一个找吗？NO，只需要告诉仓库的“管理员”，你需要什么，他就可以找给你。

与文件管理系统不同，数据库不允许用户遍历其中的内容，而在文件管理系统中，你要找某个文件就需要按照一定的索引查找文件。数据库是帮你做了这一步，只返回给你一个结果。

##### 数据库的产生

**文件管理系统的不足**

文件较少时，文件管理系统足够，当文件数量非常大时，它的不足就体现出来了。

1、数据关联度小，查询困难

2、并发操作容易混乱

3、安全性问题

4、数据不一致，冗余

**数据库的解决方案**

1. 解决冗余、不一致

以特定单位组织数据，遵守一定的规范

2. 解决查询困难、关联度小、并发操作

支持正则，通过语句就可以定位数据，加锁，不允许同时操作

3. 解决安全性问题

将每一步操作都记录下来，备份，日志

##### 数据库的发展历程

```
            数据管理 - 数据的管理
                |
                V
            数据库(DBMS) - 数据的管理和共享
               	  |
   |------------|------------|
   V			V			 V
网状数据库     层次数据库    关系数据库
(Network) (Hierarchical) (Relational)
```

##### CRUD

Create

Read

Update

Delete

增删改查，具有持久性存储的四个基本功能

##### 层次模型

类似树的结构，具有良好的查询效果，但是对与插入和删除不太友好，要想找到某字节点，必须通过双亲才可以。

```
					o
				  /   \
				 o     o
			    / \   / \
			   o   o o   o
```

##### 网状模型

类似图的结构，非层次关系，在关系型数据库出现之前，网状模型比层次模型使用较多。

```
			 ___
	o ----> |   |		 ___
			|   |		|	|
	o ----> | o | ---->  | o |
			|	|		|___|
	o ----> |___|		 ^  ^
			  ^			 |	|
			  |			 |	|
			 ___		 |	|
			| o | -------|  |
			|___| ----------|
```



##### 关系性

二维表，多张表之间通过共有的某 ID 属性建立联系

特点：

1. 数据结构单一，清晰
2. 关系规范化
3. 概念简单，操作方便 （因为是简单的二维表）

##### 选择什么数据库



## 安装、连接及配置

##### windows

**下载**

> https://dev.mysql.com/downloads/mysql/5.7.html

**安装**

找个盘和文件夹，建议把文件整理得规范些

**配置环境变量**

把安装目录下的 ==bin== 添加到系统环境变量 ==path== 中

例如：

> D:\DevTools\MySQL\bin

**注册**

在上面的文件夹中，使用 powershell 运行以下命令

```powershell
mysqld -install
```

**配置**

在安装目录下新建 ==my.ini== ，粘贴以下内容，保存退出

```tex
[mysqld]
-- 设置3306端口
port=3306
-- 设置mysql的安装目录
basedir=D:\Program Files\MySQL
-- 设置mysql数据库的数据的存放目录
datadir=D:\Program Files\MySQL\Data
-- 允许最大连接数
max_connections=200
-- 允许连接失败的次数。这是为了防止有人从该主机试图攻击数据库系统
max_connect_errors=10
-- 服务端使用的字符集默认为UTF8
character-set-server=utf8
-- 创建新表时将使用的默认存储引擎
default-storage-engine=INNODB
-- 默认使用“mysql_native_password”插件认证
default_authentication_plugin=mysql_native_password
[mysql]
-- 设置mysql客户端默认字符集
default-character-set=utf8
[client]
-- 设置mysql客户端连接服务端时默认使用的端口
port=3306
default-character-set=utf8
```

然后执行以下命令进行初始化

```powershell
mysqld --initialize
```

**root账户**

在执行命令之前，确保 mysql 服务已启动，（以管理员权限运行）

```powershell
net start mysql -- 启动mysql
net stop mysql  -- 关闭mysql
```

1. 执行 ==mysql --skip-grant-tables== 开启无密码登录
2. 打开新的 cmd，执行 ==mysql -uroot== 登录mysql
3. 执行 ==flush privileges;== 刷新权限
4. 执行 ==grant all privileges on *.* to root@'localhost' identified by "你想设置的密码";==
5. 执行 ==flush privileges;== 刷新新的 root 用户密码。
6. ==exit;== 退出mysql ，==exit== 退出cmd

## 数据库的基本操作

##### 创建数据库

创建数据库

```mysql
create database databasename;
```

##### 删除数据库

删除数据库

```mysql
drop database databasename;
```

##### 查看数据库

查看所有数据库

```mysql
show databases;
```

##### 关于字符集

字符及集最早是美国制定的ASCII，包含127个字符，占7位。随着计算机发展，越来越多国家需要指定自己的字符集，但是这不利于发布在不同国家。于是国际组织制定ISO-10646编码，用4字节表示字符，分别表示组、面、行、格，第一字节表示128个组，第二字节表示256个面，第三字节表示256个行，第四字节表示256个格，并规定每个面的最后两个码位不用。但是这个编码遭到一些美国计算机公司的反对，他们指定了16位的Unicode编码，经过谈判，决定将Unicode并入到ISO-10646的0组0面。之后两家虽发布各自的标准，但是二者是一致的。

UTF-16和UTF-8就是其两种变通表示方式。

utf8可以将ISO-10646或Unicode转换为1-4字节编码，换句话来说，utf是一种转换规则。utf8将ACSII码转换位1字节，将ISO-10646的2-4位转换为utf8的2-4位。

在指定字符集时，中国也制定了自己的字符集，有GB2312、GBK、GB18030。

GB2312：早期使用，不推荐使用。（2字节）

GBK：不是国标，但支持的很多。（2字节）

GB18030：很少，数据库支持也少见。（2-4字节）

##### 如何选用适合的字符集

1. 满足某国家地区的使用，若是不同国家或地区，使用Unicode，建议utf8。
2. 若要兼容之前使用的字符集，最好选用与之相同的。
3. 考虑空间。如果绝大部分是中文，数据量很大，建议GBK。好处：节省空间、节省数据库缓存，提高传输效率。如果是英文较多，建议utf8。
4. 如果要对字符进行大量运算，例如比较，排序，建议使用定长字符集，处理速度快。
5. 优先使用客户端都支持的字符集，减少转换。

##### MySQL中的字符集及校对规则

```mysql
-- 查看可用的字符集
show character set;
```
```mysql
-- 查看字符集及其默认校对规则
desc information_schema.character_sets;
```

MySQL 的字符集包括字符集（CHARACTER）和校对规则（COLLATION）两个概念。字符集是用来定义 MySQL ==存储==字符串的方式，校对规则则是定义了==比较==字符串的方式。字符集和校对规则是一对多的关系，MySQL 支持 30 多种字符集的 70 多种校对规则。

每个字符集至少对应一个校对规则。

校对规则命名约定：它们以其相关的字符集名开始，通常包括一个语言名，并且以 

==\_ci== （大小写不敏感）

==\_cs==（大小写敏感）

或 ==_bin==（二元，即比较是基于字符编码的值而与language 无关）结束。

```mysql
-- 查看某字符集的校对规则
show collation like 'gbk%';
show collation like 'utf8%';
-- 记得带 %                   ^

-- 查看
desc information_schema.collations;

-- 查看服务器当前的字符集
show variables like 'character_set_server';

-- 查看服务器当前的校对规则
show variables like 'collation_server';
```

也可以在 “my.ini” 中查看

可以通过修改配置文件，修改默认字符集（需重启mysql），或在启动时或在编译时指定。

```json
[mysqld]
default-character-set=gbk

或者在启动选项中指定：
mysqld --default-character-set=gbk

或者在编译的时候指定：
./configure --with-charset=gbk
```




##### 创建数据库指定字符编码及查看

```mysql
-- 查看当前数据库的字符集
show variables like 'character_set_database';

-- 查看当前字符集的校对规则
show variables like 'collation_database';
```

```mysql
-- 创建指定字符集的数据库
create database db_name default charset gbk;
```

创建的数据库可以通过 alter database 修改，之后再详细叙述。

## 表的基本操作

##### 概念

与通常意义上的表类似，包含字段和字段类型

##### 引用数据库及查看其中的表

```mysql
-- 引用数据库
use db_name;

-- 查看数据库中的表，必须先引用数据库
show tables;
```

##### 创建表

```mysql
create table tb_name (
	col_name_1 col_type_1 constraints,
    col_name_2 col_type_2 constraints,
    ...
) ENGINE=INNODB DEFAULT CHARSET utf8;

-- 实例
create table emp(
    ename varchar(10),
    hiredate date,
    sal decimal(10,2),
    deptno int(2)
);
```

创建表时需要指定字段名和字段类型，有时需要对字段的格式进行限制，例如，第一个字段，ename 是字段名，varchar 是字段类型（字符串），10 是字符串的最大长度。date 为日期，不需要约束。sal 代表薪水，是小数（浮点数），(10, 2)分别代表最大长度和小数点之后位数。

设置默认引擎和字符编码。

> 定义浮点数有三种，分别是float(M, N)、double(M, N)和decimal(M, N)
>
> M 代表整数和小数的位数
>
> N 代表小数位数
>
> 所以，必须使 M > N

##### 查看表结构

```mysql
-- 查看表定义
desc table_name;

-- 查看表的详细定义
show create table table_name \G
-- 大写 G，使字段竖着排列         ^
-- 使用 \g、\G 时不要带 ';'，否则会有错误提示
```

> 这里有个小细节：\G 之后没有 ;
>
> 因为 mysql 中，\g 和 \G 都有结束命令的作用，\g 相当于 ;。如果 \g 和 ; 同时用，即 show create table tb_name \g; 等同于 show create table tb_name ;;，后一个 ; 之前没有命令，就会有 ERROR:No query specified 的错误提示。
>
> \G 的作用是让结果垂直对齐显示

##### 删除表

```mysql
drop table table_name;
```

##### 修改表

1. 修改表中字段类型

```mysql
alter table table_name modify col_name col_type constraints;

-- 实例
alter table emp modify ename varchar(20);
```

2. 增加字段

```mysql
alter table table_name add [column] col_definition [first | after col_name];
-- 可以指定插入位置，（第一字段或某字段之后）

-- 实例
alter table emp add column age int(3);
```

3. 删除字段

```mysql
alter table table_name drop [column] col_name;

-- 实例
alter table emp drop column age;
```

4. 更改字段名

```mysql
alter table table_name change [column] old_col_name col_definition [first | after col_name]
-- 可以更改字段名和类型

-- 实例
alter table emp change age age1 int(4);
```

5. 更改字段排列顺序

之前的更改字段类型、添加字段和更改字段名，都有一个可选命令 ==[first | after col_name]==，用于更改顺序。

```mysql
-- 实例
-- 添加字段，指定插入位置
alter table emp add birth date after ename;

-- 修改字段类型，并调整位置到第一
alter table emp modify age int(3) first;
```

6. 更改表名

```mysql
alter table old_tb_name rename [to] new_tb_name;

-- 实例
alter table emp rename emp1;
```

## 数据操作

DML 语句，就是常见的插入（insert）、更新（update）、删除（delete）和查询（select）。

##### 插入数据（insert）

```mysql
insert into table_name (var1, var2, ...) values (val1, val2, ...);
-- 如果没有赋值，可以默认 NULL 或 自增 1
```
**查看表中数据**

```mysql
-- 从表中选择所有，显示出来
select * from table_name;
```

**一次性插入多条数据**

mysql 中可以一次性插入多条数据，只要多条数据之间用 "," 分隔

```mysql
insert into tablename (var1, var2, ...)
values
(val1, val2, ...),
(val1, val2, ...),
(val1, val2, ...),
(val1, val2, ...);

-- 如果按照字段顺序插入，可不带字段。
insert into tablename values
(val11, val12, ...),
(val21, val22, ...),
(val31, val32, ...),
(val41, val42, ...);
```

> 1. 字符串类型和日期类型：需要加引号 (’ ')
>     如CHAR、VARCHAR、TEXT、DATE、DATETIME、TIMESTAMP、YEAR、TIME等数据类型的数据需要加引号
> 2. 整数和浮点型的：不需要加
>     如TINYINT、SMALLINT、MEDIUMINT、INT、FLOAT、DOUBLE、DECIMAL等

##### 删除数据（delete）

**单个表**

```mysql
delete from table_name where col_name='something';
-- 从表中删除记录为 something 的全部记录
```

**多个表**

```mysql
delete t1, t2, ... from t1, t2, ... [where condition];

-- 同样可以使用别名，如果用了别名，后面condition中必须用别名
-- 实例
 delete a,b from emp a,dept b where a.deptno=b.deptno and a.deptno=3;
```

> 无论是单个表还是多个表，只要不加where，就会删除所有记录，一定要小心

##### 更新数据（update）


```mysql
update tablename set field1=value1, ... [where condition];

-- 替换掉已有的记录（根据 id），如果没有则插入
REPLACE tablename (...) VALUES (...);

-- condition : var="val"
-- 可以同时更新多个表中的数据
update table1, table2, ... set table1.f1=v1, table2.f2=v2 ..., [where condition]

-- e.g. 可以用 a 代替 emp，用 b 代替 dept
update emp a,dept b set a.sal=a.sal*b.deptno,b.deptname=a.ename where a.deptno=b.deptno;
```

##### 查询记录（select）

**查询所有记录**

```mysql
-- 查询所有字段的所有记录
select * from tb_name;
```

1. **不重复的记录**

```mysql
-- 使用 distinct
select distinct col_name from tb_name;
```

2. **条件查询**

```mysql
-- 使用 where condition
select * from tb_name where col_name=value;
```

> 除了使用 = 外，还可以用
>
> \>、<、 >=、 <=、 != like
>
> 不同条件之间用 or、and 连接，like是模糊查询，where like ‘%string%’;

3. **排序和限制**

**排序(order)**

```mysql
-- 排序使用 order by
select * from tb_name [where condition] [order by col_name1 [desc|asc], ...]
-- 必须使用查询的字段进行排序
-- 如果有多个字段，第一字段相同按第二字段排序，依次类推
```

> 默认是asc
>
> asc: 升序
>
> desc: 降序

**限制(limit)**

```mysql
select ... [limit offset_start, row_count];
select ... [limit  row_count OFFSET offset_start];
```

> offset_start: 开始位置（0 为第一条）
>
> row_count: 显示数量

4. **聚合(group)**

```mysql
select [col1, col2, ...] function_name
from tb_name
[where condition]
[group by col1, col2, ...]
[with rollup]
[having condition];
-- 实例
```

> function_name: 聚合操作（函数）
>
> 有 sum、count(*)、max、min
>
> group by: 要聚合的字段
>
> with rollup: 是否对分类聚合的结果再汇总
>
> having: 对分类的结果再进行过滤
>
> 注意：尽量用where，少用having

## 数据类型

mysql 数据类型可分为三大类，一是数值型，二是日期型，三是字符串类型。

-   数值型

数值型又分为整数和小数。整数有 TINYINT SMALLINT MEDIUMINT INT/INTEGER BIGINT，分别占 1 2 3 4 8字节。小数有 FLOAT DOUBLE DECIMAL(M, D)，分别占 4 8 字节，DECIMAL取决于 M和D的大小，M必须大于D，M 代表有效数字位数，D 代表小数位数。

-   日期和时间型

DATE TIME YEAR DATETIME TIMESTAMP

<table>
    <tbody>
        <tr>
            <th>
            	类型
            </th>
            <th>
            	大小<br>(bytes)
            </th>
            <th>
            	范围
            </th>
            <th>
            	格式
            </th>
            <th>
            	用途
            </th>
        </tr>
        <tr>
            <td>
            	DATE
            </td>
            <td>
            	3
            </td>
            <td>
            	1000-01-01/9999-12-31
            </td>
            <td>
            	YYYY-MM-DD
            </td>
            <td>
            	日期值
            </td>
        </tr>
        <tr>
            <td>
            	TIME
            </td>
            <td>
            	3
            </td>
            <td>
            	'-838:59:59'/'838:59:59'
            </td>
            <td>
            	HH:MM:SS
            </td>
            <td>
            	时间值或持续时间
            </td>
        </tr>
        <tr>
            <td>
            YEAR
            </td>
            <td>
            1
            </td>
            <td>
            1901/2155
            </td>
            <td>
            YYYY
            </td>
            <td>
            年份值
            </td>
        </tr>
        <tr>
            <td>
            	DATETIME
            </td>
            <td>
            	8
            </td>
            <td>
            	1000-01-01 00:00:00/9999-12-31 23:59:59
            </td>
            <td>
            	YYYY-MM-DD HH:MM:SS
            </td>
            <td>
            	混合日期和时间值
            </td>
        </tr>
        <tr>
            <td>
            	TIMESTAMP
            </td>
            <td>
            	4
            </td>
            <td>
                <p>1970-01-01 00:00:00/2038 </p>
                <p>结束时间是第 <strong>2147483647</strong> 秒，北京时间 <strong>2038-1-19 11:14:07</strong>，格林尼治时间 2038年1月19日 凌晨 03:14:07</p>
            </td>
            <td>
            	YYYYMMDD HHMMSS
            </td>
            <td>
            	混合日期和时间值，时间戳
            </td>
        </tr>
    </tbody>
</table>

-   字符串类型

字符串可分为三类，一是字符串，二是文本，三是二进制数据。二进制数据可存储一些图片，视频，等媒体文件。

字符串有 CHAR(0 - 255字节) VARCHAR(0 - 65535字节) 

文本有 TINYTEXT(0 - 255) TEXT(0 - 65535) MEDIUMTEXT( 0-16 777 215) LONGTEXT(0-4 294 967 295)

二进制有对应的 TINYBLOB BLOB MEDIUMBLOB LONGBLOB，大小也是对应的。

## 列属性完整性

1.  comment 列描述

为列添加描述，建议写上

```mysql
CREATE TABLE tb_name (
	col_name_1 col_type_1 constraints comment "",
	col_name_2 col_type_2 constraints comment "",
    ...
);
```

2.  NULL / NOT NULL 空 / 非空

是否允许为空，默认都是可以为空的，如果设置 NOT NULL，在添加记录时，不允许设置空值。

```mysql
CREATE TABLE tb_name (
	col_name_1 col_type_1 constraints NULL,
    col_name_2 col_type_2 constraints NOT NULL,
    ...
);
```

3.  DEFALULT 默认值

设置一个默认值，在插入时自动填充。

```mysql
CREATE TABLE tb_name (
	col_name_1 col_type_1 constraints DEFAULT "男",
    col_name_2 col_type_2 constraints NOT NULL,
    ...
);

-- 在插入时可以不用写默认字段，也可以使用 default 
insert into tablename (col1, col2) values (default, "");
```

4.  primary key 主键

表的主键具有唯一性，只能删除主键才能再次添加主键。

```mysql
CREATE TABLE tb_name (
	col_name_1 col_type_1 constraints PRIMARY KEY,
    col_name_2 col_type_2 constraints,
    ...
);

-- 复合主键，由多列共同作为主键
CREATE TABLE tb_name (
	col_name_1 col_type_1 constraints ,
    col_name_2 col_type_2 constraints,
    ...，
    PRIMARY KEY(col_name_1, col_name_2)
);

-- 修改表时添加或删除主键
ALTER TABLE tablename add primary key;
ALTER TABLE tablename drop primary key;
```

5.  auto_increment 自增

以当前的最大值 +1 作为记录填充，通常与主键一起使用

```mysql
CREATE TABLE tb_name (
	col_name_1 col_type_1 constraints PRIMARY KEY AUTO_INCREMENT,
    col_name_2 col_type_2 constraints,
    ...
);
```

6.  unique key 唯一键

// TODO

7.  unsigned 非负数

设置后值不能为负数。

```mysql
CREATE TABLE tb_name (
	col_name_1 col_type_1 constraints PRIMARY KEY AUTO_INCREMENT,
    age int(3) unsigned,
    ...
);
```



8.  zerofill 零填充

对数字最高位之前进行零填充，例如 5 -> 005

```mysql
CREATE TABLE tb_name (
	col_name_1 col_type_1 constraints PRIMARY KEY AUTO_INCREMENT,
    age int(3) zerofill,
    ...
);
```

>   使用零填充会默认使用 unsigned

## 数据库设计思维

根据阿里开发手册规定，新建数据表必须有以下几个列，分别是 

1.  id 唯一主键
2.  version 乐观锁
3.  is_delete 是否删除（伪删除）
4.  gmt_create 创建时间
5.  gmt_update 修改时间

## 多表查询

### 普通查询

多表查询可以一次查询多个表，查询结果是所有表记录数的乘积。

```mysql
-- 最简单的多表查询
SELECT * FROM table1, table2;
```

-   别名

```mysql
-- 给 列 别名，方便敲代码
SELECT
    students.id sid,
    students.name,
    students.gender,
    students.score,
    classes.id cid,
    classes.name cname
FROM students, classes;

-- 给表起别名
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c;
```

-   限制

```mysql
SELECT
    s.id sid,
    s.name,
    s.gender,
    s.score,
    c.id cid,
    c.name cname
FROM students s, classes c
WHERE s.gender = 'M' AND c.id = 1;
```

>   多表查询同样可以使用单表查询时的 筛选 限制等语句。

### 连接查询 JOIN

通过字段把两个表关联起来，然后进行查询的操作。

-   内连接

```mysql
-- 挑选字段
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
-- 
FROM students s
-- INNER JOIN 内连接（两表交集）， 连接表 classes
INNER JOIN classes c
-- 使用 两个表的字段 进行关联
ON s.class_id = c.id;
```

-   外连接

外连接又分左连接、右连接和全连接。左连接是以左表为主体，必须包含左表的所有元素，右表如果没有，则为 null。同理，右连接也是。全连接是取并集。

```mysql
-- 挑选字段
SELECT s.id, s.name, s.class_id, c.name class_name, s.gender, s.score
-- 
FROM students s
-- LEFT OUTER JOIN 左连接，同理有 RIGHT OUTER JOIN，FULL OUTER JOIN
LEFT OUTER JOIN classes c
-- 使用 两个表的字段 进行关联
ON s.class_id = c.id;
```

## 子查询

字查询其实就是查询的套娃，在查询的结果上再次查询。

```mysql
-- 外部查询
SELECT 
    lastName, firstName
FROM
    employees
WHERE
-- 内部查询 使用 ()
    officeCode IN (SELECT 
            officeCode
        FROM
            offices
        WHERE country = 'USA');
```



## 高级

### view视图

视图（view）是一种虚拟存在的表，是一个逻辑表，本身并不包含数据。作为一个select语句保存在数据字典中的。

##### 1、创建视图

```
CREATE [OR REPLACE] [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    VIEW view_name [(column_list)]
    AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```

create：创建视图

or replace：如果有则替换已有视图。

algorithm：选择视图算法，默认 undefined。

[(column_list)]：显示的表头，必须和select语句的列数相同。

select_statement：选择语句。

[WITH [CASCADED | LOCAL] CHECK OPTION]：表示视图在更新时保证在视图的权限范围之内

　　cascade是默认值，表示更新视图的时候，要满足视图和表的相关条件，更新所有视图，包括嵌套的视图

　　local表示更新视图的时候，要满足该视图定义的一个条件即可，只更新当前视图，不更新嵌套底层的视图

##### 2、查看视图

```mysql
select * from information_schema.views where TABLE_NAME='view_name';
show create view view_name;
```

>   所有的视图都放在 information_schema数据库中的views表中。
>
>   trigger同理

##### 3、删除视图

```mysql
DROP VIEW [IF EXISTS]   
view_name [, view_name] ...
```

##### 4、修改视图

###### 1、CREATE OR REPLACE VIEW

创建或替换。

###### 2、ALTER

```mysql
ALTER
    [ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]
    [DEFINER = { user | CURRENT_USER }]
    [SQL SECURITY { DEFINER | INVOKER }]VIEW view_name [(column_list)]AS select_statement
    [WITH [CASCADED | LOCAL] CHECK OPTION]
```

修改视图是指修改数据库中已存在的表的定义，当基表的某些字段发生改变时，可以通过修改视图来保持视图和基本表之间一致

###### 3、DML操作

DML(Data Manipulation Language)数据操纵语言。因为视图本身没有数据，因此对视图进行的dml操作==最终都体现在基表中==。

```mysql
update view_name set col_name1 = 'value1' where col_name2 = 'value2';
```

当然，视图的DML操作，不是所有的视图都可以做DML操作。

有下列内容之一，视图不能做DML操作：

　　①select子句中包含distinct

　　②select子句中包含组函数

　　③select语句中包含group by子句

　　④select语句中包含order by子句

　　⑤select语句中包含union 、union all等集合运算符

　　⑥where子句中包含相关子查询

　　⑦from子句中包含多个表

　　⑧如果视图中有计算列，则不能更新

　　⑨如果基表中有某个具有非空约束的列未出现在视图定义中，则不能做insert操作

##### 5、使用WITH CHECK OPTION约束 

使用 with check option，更新视图时不能违反创建视图时where子句。

默认使用  cascade，会检查所有的视图，包含嵌套的子视图。local不会检查子视图。

##### 6、定义视图时的其他选项

```mysql
CREATE [OR REPLACE]   
　　[ALGORITHM = {UNDEFINED | MERGE | TEMPTABLE}]  
　　[DEFINER = { user | CURRENT_USER }]  　　[SQL SECURITY { DEFINER | INVOKER }]
VIEW view_name [(column_list)]  
AS select_statement  
　　[WITH [CASCADED | LOCAL] CHECK OPTION]
```

###### 1、ALGORITHM：选择在处理定义视图的select语句中使用的方法

1.   UNDEFINED 自动选择使用的算法
2.   MERGE 将视图的语句与视图定义合并起来，使得视图定义的某一部分取代语句的对应部分
3.   TEMPTABLE 将视图存入临时表，使用临时表执行语句。

2、DEFINER

### transaction事务

只有使用了 InnoDB 的数据表才支持事务，事务保证成批的sql语句要么全部执行，要么全部不执行。单条sql语句被叫做隐藏事务。

#### 特性

事务有四个特性，分别是原子性，一致性，隔离性，持久性。

1.  原子性：要么全部执行，要么全部不执行。
2.  一致性：事务完成后，所有数据的状态都是一致的
3.  隔离性：如果多个事务并发执行，每个事务必须隔离。
4.  持久性：即事务完成后，对数据库数据的修改被持久化存储。

#### 开始事务

如何开始和结束事务？

使用 BEGIN 开始，使用 COMMIT / ROLLBACK 结束。

-   COMMIT 事务完成后提交，数据库被永久修改
-   ROLLBACK 回滚到开始，主动失败。

#### 隔离级别

SQL标准定义了4种隔离级别，分别对应可能出现的数据不一致的情况：

| Isolation Level  | 脏读（Dirty Read） | 不可重复读（Non Repeatable Read） | 幻读（Phantom Read） |
| :--------------- | :----------------- | :-------------------------------- | :------------------- |
| Read Uncommitted | Yes                | Yes                               | Yes                  |
| Read Committed   | -                  | Yes                               | Yes                  |
| Repeatable Read  | -                  | -                                 | Yes                  |
| Serializable     | -                  | -                                 | -                    |

-   Read Uncommitted

是隔离级别最低的一种事务级别。在这种隔离级别下，一个事务会读到另一个事务更新后但未提交的数据，如果另一个事务回滚，那么当前事务读到的数据就是脏数据，这就是脏读（Dirty Read）。

-   Read Committed

在Read Committed隔离级别下，一个事务可能会遇到不可重复读（Non Repeatable Read）的问题。

不可重复读是指，在一个事务内，多次读同一数据，在这个事务还没有结束时，如果另一个事务恰好修改了这个数据，那么，在第一个事务中，两次读取的数据就可能不一致。

-   Repeatable Read

在Repeatable Read隔离级别下，一个事务可能会遇到幻读（Phantom Read）的问题。

幻读是指，在一个事务中，第一次查询某条记录，发现没有，但是，当试图更新这条不存在的记录时，竟然能成功，并且，再次读取同一条记录，它就神奇地出现了。

-   Serializable

Serializable是最严格的隔离级别。在Serializable隔离级别下，所有事务按照次序依次执行，因此，脏读、不可重复读、幻读都不会出现。

虽然Serializable隔离级别下的事务具有最高的安全性，但是，由于事务是串行执行，所以效率会大大下降，应用程序的性能会急剧降低。如果没有特别重要的情景，一般都不会使用Serializable隔离级别。

>   如果没有指定隔离级别，数据库就会使用默认的隔离级别。在MySQL中，如果使用InnoDB，默认的隔离级别是Repeatable Read。

#### 引擎（了解）

数据库引擎有两种，一种是 InnoDB（现在默认的），另一种是 MyISAM （早年使用的）。

这两种数据库有什么区别呢？

|              | MYISAM | InnoDB          |
| ------------ | ------ | --------------- |
| 事务         | 不支持 | 支持            |
| 数据行锁定   | 不支持 | 支持            |
| 外键约束     | 不支持 | 支持            |
| 全文索引     | 支持   | 不支持          |
| 表空间的大小 | 较小   | 约为MyISAM的2倍 |

>   MyISAM 节约空间，速度较快
>
>   InnoDB 安全性高，事务处理，多用户多表操作。

除了上面的区别，还有物理方面的区别，即存储上的区别。

InnoDB 只有*.frm 文件和上级目录下的 ibdata1 文件

MyISAM有三个文件

​	*.frm 表结构文件

​	*.MYD 数据文件

​	*.MYI 索引文件

### 索引

### 触发器（trigger）

触发器是与表有关的数据库对象，在满足定义条件时触发，并执行触发器中定义的语句集合。

触发条件：insert、update、delete。在这三种事件之前和之后分别用 before 和 after。

mysql触发器只有行级，即对每行，for each row

如果有多行执行语句可以用 begin 和 end 包裹。

创建触发器

```
create trigger trigger_name
	<begin | after> <insert | update | delete>
	on table_name 
	for each row
	begin
		...
	end;
```

查看触发器

```
show triggers;
select * from information_schema.triggers \G
```

>所有触发器信息都存储在information_schema数据库下的triggers表中，可以使用SELECT语句查询，如果触发器信息过多，最好通过TRIGGER_NAME字段指定查询。

删除触发器

```
drop trigger [IF EXISTS] [schema_name.]trigger_name
```

如果插入大量数据，每一行都需要执行一次触发器，这是非常耗费时间的，所以不建议在频繁增删改的表中使用。

new 和 old

new 和old是执行触发器过程中参与更改的数据的元组。

new 在 insert 中是将要新插入或者已经新插入的数据的数据，取决于使用before还是after。

同理，在update中，new是新插入的数据。old是更改前的数据。

old在delete中是要删除的数据。

使用 new.col_name 和 old.col_name 获取涉及事件的数据。

### 流程控制语句

#### 1、IF

语句格式

```mysql
IF search_condition THEN statement_list
	[ELSEIF search_condition THEN statement_list]
	[ELSE statement_list]
END IF;
```

search_condition：判断条件语句，如果为 true，则执行THEN后面的语句。

ELSE：你懂的。

END IF：if控制语句结束标志。

if语句嵌套使用 ELSEIF，后面使用格式与IF相同。

#### 2、CASE

case相当于C语言中的switch，格式有所不同

```mysql
CASE case_value
	WHEN when_value THEN statement_list
	[WHEN when_value THEN statement_list]
	ELSE statement_list
END CASE;
```

case_value：一个变量，决定执行哪一条WHEN语句。

when_value：一个值，如果 case_value与之相同，则执行这条语句。

ELSE：相当于C语言中的defalult，如果所有when_value与case_value都不相同，则执行该条语句。

#### 3、LOOP

mysql中的循环语句用 LOOP。

LOOP只是单纯的执行循环语句，没有结束判断的语句，如果跳出循环，需要使用下面的 LEAVE。

```mysql
[begin_label:]LOOP
    statement_list
END LOOP [end_label]
```

其中，begin_label 参数和 end_label 参数分别表示循环开始和结束的标志，这两个标志必须相同，而且都可以省略；statement_list 参数表示需要循环执行的语句。

#### 4、LEAVE

LEAVE可以跳出循环，相当于break。一般搭配一个if语句判断跳出条件。

```mysql
add_num:LOOP
    SET @count=@count+1;
    IF @count=100 THEN
        LEAVE add_num;
END LOOP add num;
```

#### 5、ITERATE

break都有了，那continue呢。就是这个ITERATE。它可以跳过下面的语句，直接进入下一次循环。

```mysql
add_num:LOOP
    SET @count=@count+1;
    IF @count=100 THEN
        LEAVE add_num;
    ELSE IF MOD(@count,3)=0 THEN
        ITERATE add_num;
    SELECT * FROM employee;
END LOOP add_num;
```

#### 6、REPEAT...UNTIL

相当于do...while的相反。do...while条件为true执行循环，repeat...until条件为false执行循环。

```mysql
[begin_label:] REPEAT
    statement_list
    UNTIL search_condition
END REPEAT [end_label]
```

#### 7、WHILE

WHILE 与 REPEAT不同的是，档满足条件时，它才会继续执行循环。这个与C语言的while相同。

```mysql
[begin_label:] WHILE search_condition DO
    statement list
END WHILE [end_label]
```



### 存储过程

存储过程相当于一个没有返回参数的函数，它会预编译在服务器，使用时调用存储过程名和参数。因为它预编译，所以它比一般的sql执行语句更加快，降低服务器与客户机之间的通信量。

##### 1、创建一个存储过程

```mysql
-- 创建 定义用户
CREATE
    [DEFINER = { user | CURRENT_USER }]
  -- 存储过程名 和 参数列表
　PROCEDURE sp_name ([proc_parameter[,...]])
    [characteristic ...] routine_body
```

1.   create procedure p_name 创建存储过程名
2.   proc_parameter 参数
3.   routine_body 语句体 [begin_label:]BEGIN ... END [end_label]
4.   set @num = 1 变量赋值
1.   declare l_int int unsigned default 4000000; 变量声明

>   语句中有分号，会被误认为语句结束符号，可临时改变结束符号
>
>   delimiter $$ 
>
>   $$ 将作为结束符号
>
>   delimiter ; 
>
>   再改回来

例子：

```mysql
create procedure delete_matches (IN p_playerno INTEGER)
```



##### 2、删除一个存储过程 

```mysql
DROP PROCEDURE procedure_name();
```

##### 3、查看存储过程



### 有趣的函数

## 企业规范约束

## 杂项

##### group_concat 

