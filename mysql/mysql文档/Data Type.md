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
