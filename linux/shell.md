# **Shell脚本编程**
>## **shell脚本结构**
* **shell脚本头部:**  
    在shell脚本中，首行通过 #!bin/bash 来指定该shell脚本所使用的语法和执行的shell环境。
    ```shell
    #!/bin/bash
    #除了首行之外，其他以'#'开头的行均为注释，在执行时会被忽略
    ```
* **程序体：**    
    程序体中包含了该脚本的具体执行逻辑，并且在执行结束后，通过exit命令来返回该脚本的执行执行结果。
    ```shell
    #用exit命令来返回脚本的执行状态，若返回值status为0,代表正常退出
    exit status
    ```
>## **Shell语法**
* **输出shell脚本中定义的变量或是环境变量：**   
    可以通过${VARNAME}的形式来输出变量的值，例如：
    ```shell
    #注意”“和‘’的区别，
    #   当使用”“来包围字符串时，字符串中的$(expression)和${varname}
    #       都会被替换为变量值和执行结果
    #   而当使用‘’来包围字符串时，$(expression)和${varname}并不会被
    #       替换为执行结果，而是以字面量的形式输出
    echo "PATH VALUE IS : ${PATH}
    ```
* **${expr}的使用：**   
    1. 字符串的摘取：   
        * '#'符号用来去掉左前缀的部分，’#‘作用于于第一个匹配，’##‘作用于最后一个匹配
        ```shell
        #设置路径为/var/log/mysql.log
        export VARPATH=/var/log/mysql.log
        
        #通过 '#*/'去掉第一'/'字符之前的子串
        #输出结果为var/log/mysql.log
        echo ${VARPATH#*/}

        #通过'##*/'来去掉最后一个'/'字符之前的子串
        #输出结果为mysql.log
        echo ${VARPATH##*/}
        ```
        * '%'符号用来去掉右后缀的部分，'%'作用于从右往左的第一条匹配，而'%%'则作用于从右往左的最后一条匹配
        ```shell
        #通过'%/*'可以去除最后一个'/'字符之后的部分
        #输出结果为/var/log
        echo ${VARPATH%/*}

        #通过'%%/*'可以删除第一个’/'之后的部分
        #结果为空串
        echo ${VARPATH%%/*}
        ```
        * 可以通过'\${\${expr1}expr2}'对前一次的操作结果进行再一次操作，例如
        ```shell
        #设置路径为/etc/init.d/idea.tar.gz
        export VARPATH=/etc/init.d/idea.tar.gz

        #截取文件扩展名tar.gz
        #输出结果为tar.gz
        echo ${${VARPATH##*/}#*.}
        ```
    2. **字符串子串的截取和替换**   
        * \${STR:from:len}可以截取字符串从第from个字符开始，长度为len的子串
        ```shell
        export STR=/var/log/mysql.log

        #截取第6到第8个字符，结果为log
        echo ${STR:5:3} 
        ```
        * 可用/src/target来将字符串中第一个src子串替换为target，用//src/target可以将字符串中所有的src子串替换为target
        ```shell
        export STR='linux is not unix'

        echo ${STR/n/N} #输出liNux is not unix,替换第一个‘n’
        echo ${STR//n/N} #输出liNux is Not uNix，替换所有‘n’
        ```
        * 字符串长度可用${#STR}来获取
        ```shell
        export STR=linux

        echo ${#STR} #输出结果为5

        ```
    3. **根据状态为字符串赋值**     
        * ${VAR-'value'}:如果当前VAR没有设置，则返回‘value'值
        * ${VAR:-'value'}:如果当前VAR没有设置或为空串，返回'value'值
        * ${VAR+'value'}:如果当前VAR没有设置，返回空值，否则返回'value'值
        * ${VAR='value'}:如果当前VAR没有设置，将VAR设置为'value'，并且返回'value'
        * ${VAR:='value'}:如果当前VAR没有设置或者为空串，将VAR设置为'value',并且返回'value'

    4. **数组**
        * 可以通过（ele1 ele2 ele3 ...)来定义数组
        * 可以通过\${ARR[i]}来访问数组中的元素
        * ${ARR[@]}或者${ARR[*]}可以返回数组中所有的元素
        * 获取数组中元素的个数可以使用如下表达式
            ```shell
            ${#ARR[@]}
            #注意，获取数组中元素数量必须用${#ARR[@]}而不是${#ARR}，
            #在bash中，${ARR}只会返回ARR数组的第一个元素，
            #在bash中，${#ARR}也只会返回数组中的第一个字符串的字符数量
            ```
        * 在bash中，向数组中添加元素需要用如下方式
            ```shell
            ARR[${#ARR[@]}]=value
            ```


* **整数运算**  
    * 在shell脚本中，可以使用 var=$((expr)) 来执行浮点数运算和赋值，但是，在bash中，expr仅支持整形的运算，并不支持浮点数运算，而zsh中expr既可以是整形也可以是浮点型运算
    ```shell
    #在bash中，可以通过bc和管道来实现浮点数的运算
    #注意在使用bc命令执行浮点数运算时，必须指定scale精度，否则起默认情况下并不保留小数
    echo "scale=3;1.0/3" | bc
    ```
* **test命令和条件判断**    
    * test命令可以用于判断表达式为true/false    
        1. 文件判断：   
        * -e : 文件是否存在
        * -f : 文件是否存在且为普通文件
        * -d : 文件是否存在且为目录类型
        * -b : 文件是否存在且是块设备
        * -c : 文件是否存在且是字符型设备
        * -S : 文件爱呢是否存在且为sock
        * -p : 文件是否存在且为FIFO（pipe）
        * -L : 文件是否存在且为链接（link）
        2. 权限判断：
        * -r : 文件是否存在且对其有读权限
        * -w : 文件是否存在且对其有写权限
        * -x : 文件是否存在且对其有执行权限
        * -s : 文件是否非空白，若为空文件，返回为false
        3. 文件之间的比较：
        * f1 -ef f2 : 比较f1和f2两个文件是否是同一个文件
        * f1 -ot f2 : 比较f1文件的mtime是否比f2文件老
        * f1 -nt f2 : 比较f1的mtime是否比f2新
    * test命令用来比较整数（浮点数无效）    
        * a -eq b : 比较a和b是否相等
        * a -ne b : 比较a和b是否不等
        * a -lt b : 比较a是否小于b
        * a -gt b : 比较a是否大于b
        * a -le b : 比较a是否小于等于b
        * a -ge b : 比较a是否大于等于b
    * test判断字符串    
        * -n : 判断字符串是否不为空
        * -z : 判断字符串是否为空
        * == : 判断字符串是否相等
        * != : 判断字符串是否不相等
    * 多重条件判断  
        * -a : and
        * -o : or
        * ! : not
    * 可以用[]来代替test命令，例如 test a -eq b 可以被替换为[ a -eq b ]，注意[ expr ]中'['和expr之间、expr和']'之间都需要有空格分隔
        * 通常，在test或者[ expr]表达式中使用\${VARNAME}时，都需要用" "将起扩起来，因为\${VARNME}可能在字符串中包含空格,例如"touma kazua"，若是不用”“将其包含，那么起将被命令行识别为两个参数，会导致too many arguments异常
* **shell传参**     
    * shell脚本可以在脚本名之后附加参数，如 mysum.sh  1 2 ,其中 $0 为 mysum.sh ,$1 为1,$2 为2,以此类推。还有如下特殊参数：    
        * $# : 除了脚本名称之外的参数个数
        * $@ : 除了脚本名之外其他参数组成的数组,各个变量仍然独立
        * $* : 除了脚本名之外所有参数聚合成的字符串，默认使用空格分隔
* **Shell条件判断**
    * if-then结构：
    ```shell
    if [ expr ];then
        some operations...
    fi
    ```
    * if-else-if结构：
    ```shell
    if [ expr1 ];then
        some operations...
    elif [ expr2 ];then
        some operations...
    elif [ expr3 ];then
        some operations...
    else
        some operations...
    fi
    ```
    * switch-case结构：
    ```shell
    case ${var} in
    "str1")
        some operations...
        ;;
    "str2")
        some operations...
        ;;
    "str3")
        some operations...
        ;;
    *)
        some operations...
        ;;
    esac
    ```
* Shell函数
    * Shell函数可以通过function关键字来创建，格式如下
    ```shell
    function FunctionName()
    {
        Function Body...
        #函数中也可以通过$1、$2来获取传递给函数的参数
    }

    #函数调用
    FunctionName arg1 arg2 ...
    #函数内通过$1只能读取传递给函数的参数变量，而无法读取shell程序中的参数变量
    ```
* Shell循环
    * while循环
    ```shell
    #while循环结构
    while [ expr]
    do
        some operations...
    done
    ```
    * until循环
    ```shell
    until [ expr ]
    do
        some operations...
    done
    ```
    * for-in循环
    ```shell
    #可以用seq命令指定首数/尾数/增量，以便进行1～n的遍历
    #seq 尾数
    #seq 首数 尾数
    #seq 首数 增量 尾数
    for var in obj1 obj2 obj3...
    do
        some operations...
    done
    ```
* Shell语法检测
    * 可以使用sh -n来检测shell脚本的语法是否正确