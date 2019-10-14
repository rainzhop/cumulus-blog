---
title: mysql-tutorial-by-tencent-cloud
tags: 数据库, mysql
---

腾讯云搞数据库诊断大赛，提供了一份入门课程，这就拿来复习一下。

### MySQL数据类型

数据类型主要三大类：数据类型、日期/时间类、字符串类型

#### 数值类型

##### 整数

| 证书类型        | 字节数 |
| --------------- | ------ |
| `TINYINT`       | 1      |
| `SMALLINT`      | 2      |
| `MEDIUMINT`     | 3      |
| `INT`/`INTEGER` | 4      |
| `BITINT`        | 8      |

###### 有关显示宽度

显示宽度通过类型后的小括号内的数字`M`设定。

当插入数字宽度大于`M`时，显示时能够完全显示。

当插入数字宽度小于`M`时，显示时显示宽度为 `M`。

###### 有关UNSIGNED

在 MySQL 数据库中，对于UNSIGNED数值的操作，其返回值都是UNSIGNED 的。在UNSIGNED数值的操作下，要获得-1，需要对SQL_MODE参数进行设置：

```sql
> set sql_mode='NO_UNSIGNED_SUBTRACTION';
```

建议还是不要使用UNSIGNED，因为通常INT解决不了的问题，使用UNSIGNED INT同样解决不了，而且还会引入其他问题。

但存储IP时，需要使用UNSIGNED。用INT UNSIGNED存储 IP 地址，且用`INET_ATON()`、`INET_NTOA()`进行转换。

###### 有关ZEROFILL

使用ZEROFILL参数时，MySQL会自动添加UNSIGNED属性。

ZEROFILL是个显示属性，在显示时为数字前填充`0`。

##### 浮点数和定点数（小数）

| 类型                             | 字节数 |
| -------------------------------- | ------ |
| `FLOAT`                          | 4      |
| `DOUBLE`/`REAL`                  | 8      |
| `DECIMAL(M,D)`/`NUMERIC`/`FIXED` | M+2    |

###### 有关精度

M是数据位数（精度）的总数（小数点不算位数），D是小数点（标度）后面的位数。

需要高精度存储时，不要使用`FLOAT`和`DOUBLE`类型，使用`DECIMAL`。

##### 位值

| 类型     | 字节数  |
| -------- | ------- |
| `BIT(M)` | (M+7)/8 |

##### 布尔值

| 类型                          | 字节数 |
| ----------------------------- | ------ |
| `BOOLEAN`/`BOOL`/`TINYINT(1)` | 1      |

MySQL数据库产品没有真正实现对布尔类型的支持，在使用的时候，它和普通的TINYINT一样。

#### 日期与时间类型

| 数据类型    | 字节数 | 取值范围                                |
| ----------- | ------ | --------------------------------------- |
| `YEAR`      | 1      | 1901-2155                               |
| `DATE`      | 3      | 1000-01-01~9999-12-31                   |
| `TIME`      | 3      | -838:59:59~838:59:59                    |
| `DATETIME`  | 8      | 1000-01-01 00:00:00~9999-12-31 23:59:59 |
| `TIMESTAMP` | 4      | 19700101000001~20380119031407           |

##### YEAR

格式：

* YYYY

插入2000年时需要用字符型`'0'`或`'00'`，否则会变成`'0000'`。

##### TIME

不仅可以用来表示时刻，还可以表示时间间隔。

格式：

- D HH:MM:SS.fraction
- HH:MM:SS.fraction
- HH:MM:SS
- HH:MM
- D HH:MM:SS
- D HH:MM
- D HH
- SS
- 不能是 MM:SS

D表示日，取值范围0<=D<=34。

##### DATETIME

格式：

* YYYY-MM-DD HH:MM:SS
* YY-MM-DD HH:MM:SS
* YYYY/MM/DD HH+MM+SS
* YYYYMMDDHHMMSS
* ...

任何标点符都可以用做日期部分或时间部分之间的间割符，也可以不使用间隔符。

##### DATE

格式：

* YYYY-MM-DD
* YY-MM-DD

MySQL规定，日期总以“年-月-日”为顺序。

##### TIMESTAMP

格式：

* YYYY-MM-DD HH:MM:SS

###### 自动初始化

DEFAULT CURRENT_TIMESTAMP

插入时如未给出具体值，则自动取值为当前时间戳。

###### 自动更新

ON UPDATE CURRENT_TIMESTAMP

修改当前行数据时，其中的TIMESTAMP列将自动更新值为当前时间戳。

自动初始化属性或自动更新属性仅能添加到一列数据。

#### 字符串类型

| 类型           | 定义                         | 占用字节数（N代表一个字符占用的字节数）      |
| -------------- | ---------------------------- | -------------------------------------------- |
| `CHAR(M)`      | 定长字符串                   | M\*N                                         |
| `VARCHAR(M)`   | 变长字符串                   | M\*N + 1 或 M\*N + 2（1和2表示字符串的长度） |
| `BINARY(L)`    | 定长二进制串（无字符集）     | L                                            |
| `VARBINARY(L)` | 变长二进制串（无字符集）     | L+1                                          |
| `TEXT`         | 存储超长文本                 | M\*N+2                                       |
| `TINYTEXT`     |                              | M\*N+1                                       |
| `MEDIUMTEXT`   |                              | M\*N+3                                       |
| `LONGTEXT`     |                              | M\*N+4                                       |
| 各种`BLOB`     | 存储超长二进制串（无字符集） | L+？                                         |
| `ENUM`         | 枚举                         | 1或2                                         |
| `SET`          |                              |                                              |

#### 高频使用类型

* 数值类型中，整形和DECIMAL用得多。
* 日期和时间类型中，DATETIME和TIMESTAMP用得多，性能要求高的用TIMESTAMP多一些。
* 字符串类型中，CHAR和VARCHAR用得多，TEXT在存储大量文本时用得较多。不建议使用BLOB。ENUM和SET用来存储性别、星期等还可以，如status、type一类的值，使用TINYINT更好。

### MySQL索引



