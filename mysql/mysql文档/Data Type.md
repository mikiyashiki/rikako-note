# Mysql Data Type
## Mysql数字类型语法
### 整型数据类型
对于整型数据类型，M代表最大的显示宽度。该最大显示宽度是255。显示宽度和该整型类型可以存储范围的值没有关系。***在mysql 8.0.17中，整型的M值已经被遗弃。***
```sql
# 将M限定为4并不会限制INT类型可以存储值的范围
INT(4)
```
> 将M限定为4时，当应用使用该字段整型值时，如果该字段值显示宽度小于M，会在左边填充空格

### 非整型数据类型
对于浮点类型和fixed-point数据类型，M代表该字段总共能够存储多少位，M为能存储的最多位长度（十进制）。

### zerofill
对于数字类型的类型，可以为其指定zerofill，在指定为zerofill后，会为其自动添加unsigned约束，该数字类型字段存储的值不论是整型还是浮点数均需要为非负值。
```sql
# 被zerofill修饰的字段，如果显示宽度小于M，那么都会在左边填充0

# 其等同于INT(10) UNSIGNED ZEROFILL
INT(10) ZEROFILL

DECIMAL(28,2) ZEROFILL
```
***在mysql8.0.17，zerofill对于数值类型字段也被遗弃了***

### unsigned
对于数值类型的字段，可以支持unsiged和signed属性。默认情况下数值类型是signed的，可以手动将其指定为unsigned。
***在mysql8.0.17，对于float、decimal、double类型，unsigned属性也被遗弃了。可以通过简单的check来进行替换***

### serial
serial是一个对于BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE的alias。
```sql
# 如下字段定义
id serial
# 等同于
id bigint unsigned not null auto_increment unique
```

### BIT[(M)]
bit值类型，M代表每个值的bit位数，M值可以从1到64，默认情况下若M省略，M为1

### TINYINT[(M)] [UNSIGNED] [ZEROFILL]
8位整型，有符号范围为-128到127，无符号范围为0到255
### BOOL,BOOLEAN
该类型等同于TINYINT(1)，值为0代表false，值非0代表true
### SMALLINT[(M)] [UNSIGNED] [ZEROFILL]
16位整型，有符号范围为 -32768到32767，无符号范围为0 到 65535
### MEDIUMINT[(M)] [UNSIGNED] [ZEROFILL]
24位整型，有符号范围为-8388608到8388607，无符号范围为 0 到16777215
### INT[(M)] [UNSIGNED] [ZEROFILL]
32位整型，有符号范围为-2147483648 到2147483647，无符号范围为0 到4294967295
### BIGINT[(M)] [UNSIGNED] [ZEROFILL]
64位整型，有符号范围为 -9223372036854775808到9223372036854775807，无符号范围为0到18446744073709551615
> ***所有算数都是使用带符号的bigint或者double进行运算，故而不要使用bigint大于 9223372036854775807 （63bit）***

### DECIMAL[(M[,D])] [UNSIGNED] [ZEROFILL]
fixed-point nubmer，M是非整型的总共的位数，而D对应的则是小数点之后的位数。小数点和负的符号并不包含在M中。  
如果D是0，则代表该数没有小数部分。***对于DECIMAL来说，M最大值为65，D受支持的最大值为30，如果D省略，则D默认为0，若M省略，那么M的默认值为10

### FLOAT[(M,D)] [UNSIGNED] [ZEROFILL]
单精度浮点数，M为非整型总共的位数，D为小数点之后的位数。如果M和D省略，那么会根据硬件的限制来存储。单精度浮点数大概精确到小数点后7位。
> ***FLOAT(M,D)是一个非标准的mysql拓展，在mysql 8.0.17中，该语法被废弃***

### DOUBLE[(M,D)] [UNSIGNED] [ZEROFILL]
双精度浮点数，其精度大概精确到小数点后15位

### DECIMAL和NUMERIC
DECIMAL和NUMERIC用于存储高精度的数值类型，当保存数字的精度很重要时需要使用这些类型，如货币数据。在mysql中，NUMERIC类型被实现为DECIMAL

### BIT
对于bit类型，其赋值可以使用b'value'，其中value为二进制类型。
```sql
# 其返回值为true
select b'011' = 3;
```
## mysql日期和时间类型
### 日期和时间类型语法
对于time、datetime、timestamp类型，mysql允许second（秒）具有小数部分，最高精度到小数点后6位（微秒）。
> ***time、datetime、timestamp定义小数部分格式如下***  
> ***type_name(fsp)***
> - type_name为date、datetime、timestamp中的一个
> - fsp为second的小数部分精度
```sql
CREATE TABLE t1 (t TIME(3), dt DATETIME(6), ts TIMESTAMP(0));
```
对于fsp，其必须位于0~6的范围内，0代表其没有小数部分。若fsp省略，则默认情况下fsp为0.
### date
支持的日期是'1000-01-01'到'9999-12-31'。mysql通过'YYYY-MM-DD'的格式来显示date数据，但是对于date类型的赋值，既可以通过字符串类型也可以通过数字类型。
### datetime（fsp）
***datetime***类型是***date***和***time***类型的组合。datetime类型支持的范围是 '1000-01-01 00:00:00.000000'到'9999-12-31 23:59:59.999999'，mysql展示datetime类型值是通过'YYYY-MM-DD hh:mm:ss[.fraction]'格式。同样的，datetime类型的赋值可以通过字符串类型和数字类型。
### timestamp（fsp）
时间戳类型。timestamp类型支持的范围是'1970-01-01 00:00:01.000000' UTC 到 '2038-01-19 03:14:07.999999' UTC。timestamp类型的值存储着从('1970-01-01 00:00:00' UTC)其经过的秒数。
> ***timestamp类型不能表示'1970-01-01 00:00:00'，因为该时间点等于timestamp类型的计时起点，但是timestamp类型中0已经被预留来代表 '0000-00-00 00:00:00'*** 

> ***explicit_defaults_for_timestamp已启用***  
> 如果 explicit_defaults_for_timestamp 属性已经被启用，那么不会将CURRENT_TIMESTAMP赋值给timestamp字段做默认值，也不会在更新时自动将CURRENT_TIMESTAMP赋值给timestamp字段。并且，任何没有声明为not null的timestamp字段都是可为空的。

> ***explicit_defaults_for_timestamp未启用***
> 除非被显式指定，否则数据表中的timestamp列都会被赋值为最近修改的datetime。该属性开未开启时，可以通过timestamp字段记录上次update或insert时间。  
> 可以通过为timestamp字段赋值一个null来将该字段设置为当前时间，除非该字段在定义时通过NULL属性声明该字段可以为空。

> ***自动赋值为当前时间和记录更新时间设置***
> 可以在定义该timestamp列字段时指定DEFAULT CURRENT_TIMESTAMP和ON UPDATE CURRENT_TIMESTAMP,为该timestamp字段指定默认值和记录最近更新时间。默认情况下，第一个timestamp列字段具有这些属性，数据表中的任何timestamp字段也可以手动指定这些属性。

### time(fsp)
time类型的范围为'-838:59:59.000000'到'838:59:59.000000'，msyql显示time类型的格式为'hh:mm:ss[.fraction]'，但是对time类型的赋值既可以用字符串类型也可以用数字类型。
### YEAR(4)
year类型用来表示年，year类型通过YYYY格式进行展示，为year类型字段赋值既可以通过字符串类型也可以通过数字类型。

> 对于时间类型，avg和sum等聚合函数不起作用。该类聚合函数会将值转化为数字，并在遇到第一个非数字字符时转换失败。如果想要对时间类型使用聚合函数，需要在使用前将时间类型转化为数字单元，并在聚合后将数字单元转化为时间类型，如下：
> ```sql
> SELECT SEC_TO_TIME(SUM(TIME_TO_SEC(time_col))) FROM tbl_name;
> SELECT FROM_DAYS(SUM(TO_DAYS(date_col))) FROM tbl_name;
> ```
