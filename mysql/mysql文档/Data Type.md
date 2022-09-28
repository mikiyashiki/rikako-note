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
> 可以在定义该timestamp列字段时指定DEFAULT CURRENT_TIMESTAMP和ON UPDATE CURRENT_TIMESTAMP,为该timestamp字段指定默认值和记录最近更新时间。***若explicit_defaults_for_timestamp未启用,默认情况下，若第一个timestamp列没有指定default和on update属性中的任何一个，第一个timestamp列字段具有这些属性，数据表中的任何timestamp字段也可以手动指定这些属性。***

### time(fsp)
time类型的范围为'-838:59:59.000000'到'838:59:59.000000'，msyql显示time类型的格式为'hh:mm:ss[.fraction]'，但是对time类型的赋值既可以用字符串类型也可以用数字类型。
### YEAR(4)
year类型用来表示年，year类型通过YYYY格式进行展示，为year类型字段赋值既可以通过字符串类型也可以通过数字类型。

> 对于时间类型，avg和sum等聚合函数不起作用。该类聚合函数会将值转化为数字，并在遇到第一个非数字字符时转换失败。如果想要对时间类型使用聚合函数，需要在使用前将时间类型转化为数字单元，并在聚合后将数字单元转化为时间类型，如下：
> ```sql
> SELECT SEC_TO_TIME(SUM(TIME_TO_SEC(time_col))) FROM tbl_name;
> SELECT FROM_DAYS(SUM(TO_DAYS(date_col))) FROM tbl_name;
> ```

### mysql中timestamp的存储
在mysql中，timestamp在存储时，会从本地时区转化为UTC时区然后再存储到数据库中，在获取存储的timestamp时会从UTC时区转化为本地时区（该行为在其他类型，如datetime，中则不会发生）。
### mysql中的时区
在默认情况下，对于每个Connection，当前时区为mysql server的时区。但是，Connection时区也可以针对每个Connection单独设置（在jdbc:mysql://{server}:{port}/{db_name}?{追加参数}中单独设置）。
> 由于时区统一转化为UTC后再存储于数据库中，那么在存储了timestamp之后，若再次访问timestamp时时区发生了改变，那么将存储的UTC时区timestamp转化为Connection对应时区时，转化后时区时间和存储时的时区时间并不相同。

### time、datetime和timestamp传入无效值时
对于无效的time、datetime、timestamp值，会将其转化为该类型默认的‘0’值，例如'0000-00-00'或者'0000-00-00 00:00:00'。

### tmestamp转化为datetime
可以通过cast()函数和at time zone操作符将timestamp类型转化为datetime类型。
```sql
SELECT col,
         CAST(col AT TIME ZONE INTERVAL '+00:00' AS DATETIME) AS ut
         FROM ts ORDER BY id;
```

### mysql中date部分
- 在mysql中，对于date类型或者timestamp或datetime中的date部分，任何标点符号都可以作为分隔符
  ```sql
  '10:11:12' 代表 '2010-11-12'
  '10:11:12 00:00:00' 代表 '2010-11-12 00:00:00'
  ```
- 当date中年份只包含两位数时，若年份为00~69，则会变为2000~2069，若年份为70~99，则年份为1970~1999

### time类型
mysql中获取和展示time类型的格式为'hh:mm:ss'。time的范围为'-838:59:59' 到 '838:59:59'。
> ***time范围***  
> 之所以time的范围会如此之大并且支持负值，是因为time类型不仅可以用来表示一天中的时间点，还可以用来表示两个时间点之间的间隔（间隔可能超过24h，也有可能为负值）

### time类型的缩写
- 缩写中带有冒号':'时，值将会被解释为一天中的时间点，并且，’11:12'将会被解释为'11:12:00'而不是'00:11:12'
- 缩写中不带冒号时，将会被解释为两个时间点之间的间隔。并且，最右边的两个数将会被解释为秒，'1112'和1112也会被解释为'00:11:12'

### year类型
在mysql 8.0.19中，不再支持year(4)这种显式指定宽度的方式已经被废弃，并且，过去支持的year(2)类型在8.0.19中也不再被支持。
> year类型支持的范围是'1901'到'2155'，也可通过数字类型1901到2155指定

### timestamp类型和datetime类型自动初始化和更新
对于timestamp类型和datetime类型，可以被自动初始化和更新到当前的时间。  
> 当对timestamp或者datetime字段设置了on update current_timestamp时，若某行中任一其他列的值发生变化，该列变化前后值不同，那么设置了on update的datetime或timestamp字段会更新到当前时间。若update前后所有的列值都与修改之前相同，那么即使执行了update操作，on update列的时间也不会更新。  
> ***如果想要防止设置了on update的列在其他列被修改时更新为当前时间，可以显式将该列设置为该列当前的值。***
> ```sql
> # 即使dt被设置了on update，id变更后dt仍然为当前的值
> update dt=dt,salary+=10 wherre id = 1000

> ### current_timestamp的同义词
> 如下同义词都和current_timestamp具有相同的含义
> - current_timestamp()
> - now()
> - localtime
> - localtime()
> - localtimestamp
> - localtimestamp()

### timestamp或datetime指定小数位精度
如果timestamp或者datetime类型在声明时使用了小数位精度，那么在current_timestamp后也要指定相同的精度。
```sql
CREATE TABLE t1 (
  ts TIMESTAMP(6) DEFAULT CURRENT_TIMESTAMP(6) ON UPDATE CURRENT_TIMESTAMP(6)
);
```

### explicit_defaults_for_timestamp
当explicit_defaults_for_timestamp关闭时，timestamp默认情况下是not null的，该类字段不能含有null值，若将字段显式设置为null时，字段值实际被设置为当前时间。若想要允许该字段包含null，可以在声明该字段时显式指定null属性，此时默认值也会变为null。  
当explicit_defaults_for_timestamp启用时，只有指定timestamp字段null属性时该字段才能为空。并且，在explicit_defaults_for_timestamp启用时不可以通过赋值字段为null的形式来将timestamp字段设置为当前值。
> ***当explicit_defaults_for_timestamp开启时，默认条件下timestamp类型声明会自动添加null default null***

### 时间函数返回带小数位的时间
默认情况下，now()或current_timestamp()函数返回当前的时间戳，如果参数为空，返回结果并不含有小数位。如果想要为now()或者current_timestamp()函数的返回结果指定小数位，可以为函数参数指定一个0~6的整型参数用来限制返回结果时间的小数点位数。
```sql
# 返回时间戳带有4位小数
select current_timestamp(4);
```

### mysql中时间类型之间的转换
#### date类型
- date类型转化为datetime或timestamp类型时，时间类型将会添加'00:00:00'部分
- date类型转化位time类型时并没有用处，只会转化为'00:00:00'
#### datetime类型和timestamp类型
- 当datetime类型或timestamp类型转化为date类型时，会考虑time部分秒的小数部分并且对time部分进行舍入，若time部分为‘23:59:59.499’会舍入到当天，而‘23:59:59.500’则是会舍入到下一天。
 ```sql
 '1999-12-31 23:59:59.499' -> '1999-12-31' # 舍入到当天
 '1999-12-31 23:59:59.500' -> '2000-01-01' # 舍入到下一天
 ```
- 当datetime或timestamp类型转化为time类型时，会丢弃date部分
#### time类型
- 如果time类型转化为datetime类型或是timestamp类型，current_date()返回的date将会被用作date部分。***并且，time部分会看作时间段，并且附加在current_date()返回的date部分上。如果time类型的值范围位于'00:00:00'和'23:59:59'之外，那么date部分会根据time部分的值向前或向后变化。***
```sql
# 若当前current_date()的返回值为'2012-01-01'

# 当time为'12:00:00'时，转化成的datetime为
'2012-01-01 12:00:00'

# 当time为'24:00:00'时，转化成的datetime为
'2012-01-02 00:00:00'

# 当time为'-12:00:00'时，转化成的datetime为
'2011-12-31 12:00:00'
```
- 当time类型转化为date时，逻辑和上面一样，但是会舍弃得到datetime结果的time部分，即结果为'2012-01-01'、'2012-01-02'或'2011-12-31'
