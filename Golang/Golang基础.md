# Golang基础
- ## Golang中的包机制
  - Golang程序是由包组成的，并且在名为main的包中开始运行
  - 包的导入
    - 包可以通过import语句进行导入，在导入多个包时，可以用()将导入的多个包扩起来，并且每个包占据一行
    ```Golang
    import （
        “fmt"
        "math"
        )
    ```
 - 包的导出
   - 在包中，如果一个名字以大写字母开头，那么这个名字将会被导出，如math.Pi会被导出，而math.pi则不会被导出
   - 在导入包时，只能够引用那些包中被导出的变量
- ## Golang中函数
  - 在Golang中，函数可以接受一个或者多个参数，但是函数参数中参数类型位于形参名称之后
  ```Golang
  func f(msg string,n int) {
      fmt.Println(msg,n)
  }
  ```
  - 在Golang中，连续的函数参数如果都是同一种类型，那么这些连续参数的类型都可以省略，但是在相同类型的最后一个参数末尾要指明参数类型
  ```Golang
  func f(a,b int,msg string) {
      fmt.Println(a,b,msg)
  }
  ```
  - Golang函数的多值返回
    - Golang函数可以返回任意数量的返回值
  ```Golang
  func f() (string,string) {
      return "fuck","shit"
  }

  a,b:=f()
  // a:"fuck",b:"shit"
  fmt.Println(a,b)
  ```
  - Golang函数可以为返回值进行命名，其会被视作在函数顶部定义的变量，可以在函数体中引用变量并且对其进行修改
  - Golang函数中如果使用了命名返回值，那么在return语句后没有跟随其他值时，会将命名返回值变量当前的值
  ```Golang
  func f() (retVal int) {
      // retVal值默认是0
      fmt.Println(retVal)
      retVal=20
      // return语句会默认返回retVal变量的值，返回值为20
      return
  }
  ```
- ## Golang中的变量
  - Golang中变量的声明可以通过var关键字，并且在i变量名之后跟随类型关键字
  ```Golang
  var a int
  ```
  - Golang在声明变量时，可以为变量指定一个初始化器，在为每个变量指定初始化器后，变量的类型可以被省略
  ```Golang
  var a,b,c=1,2,"kazusa"
  fmt.Println(a,b,c)
  ```
  - Golang允许在函数内，通过:=符号来声明一个变量，并且其变量的默认类型为:=后跟随的值类型
  ```Golang
  // inside a func
  a := 1
  fmt.Println(a)
  // 在函数外部，这种变量声明的方式不被允许
  ```
- ## Golang中的基本类型
  - Golang中基本类型如下：
    - bool
    - string
    - int int8 int16 int32 int64
    - uint uint8 uint16 uint32 uint64 uintptr
    - byte （和uint8相同）
    - rune （和uint32相同，用来代表unicode字符码位）
    - float32 float64
    - complex64 complex128 （复数）
    ```Golang
    // 在Golang中，可以采用和import一样的格式来声明
    //  多个不同类型的变量
    var (
        a,b int=1,2
        msg string="fuck"
    )
    ```
  - 各类型变量在未赋初始值时的默认值
    - 数值型（整数、浮点数） ： 0
    - bool： false
    - string ： 空字符串
  - Golang中的类型转换
    - 在Golang中，在变量声明时，如果显式指定了变量的类型，那么在后续为该变量指定初始化器或者赋值时，等式右边的类型必须和等式左边的类型相同，否则应该显式制定类型转换
    ```Golang
    var a int=1
    var b int8=int8(a)
    ```
  - Golang中类型推断
    - 如果在声明变量时，没有为变量显式指定类型，那么将会根据等式右边的值来推断变量类型
      - 通过 := 表达式通过等式右边表达式进行推断
      - 在var关键字右边通过初始化器来推断类型
    ```Golang
    // 默认情况下，整数类型的字面量将会被推断为int
    // 浮点数类型的字面量将会被推断为float64
    // 复数类型将会被推断为complex128
    var a = 1 // a被推断为int类型
    var f = 1.0 // f被推断为float64类型
    ```
- ## Golang中的常量
  - Golang中常量的声明和变量类似，但是使用const关键字
  - 常量可以是bool、string、数值、字符
  - 常量的声明无法使用 ：= 方式
  ```Golang
  const (
      a,b int=1,2
      c string="shiro"
  )
  fmt.Println(a,b,c)
  ```
- ## Golang中的循环
  - 在Golang中，只存在有一种循环，for循环。
  - Golang中for循环结构如下：
  ```Golang
  // 其结构类似与java和c中的for循环
  //    其中，init和post部分是可选的
  //    for ;condition expression; {}   
  for init statement;condition expression;post statement {
      // loop body
  }
  ```
  - 在Golang中while循环结构如下：
  ```Golang
  for expression {

  }
  ```
  - 在Golang中，如果想实现无限循环，expression可以被省略
  ```Golang
  for {
      // infinite loop
      
  }
  ```
- ## Golang中的条件分支
  - 在Golang中，if的条件表达式不需要用小括号扩起来，但是if后跟的语句块必须用大括号包起来
  ```Golang
  if condition-A-expression {
      // condition A
  } else if condition-B-expression {
      // condition B
  } else {
      // condition else
  }
  /**
    *  此时else if/else和上一个条件的'}'符号必须位于同一行
  **/
  ```
  - 在Golang中的if语句中，可以在条件表达式之前添加一个执行语句，在执行语句中定义的变量只能在if和else if/else块中被访问
  ```Golang
  if msg:="msg content";expr1 {
      fmt.Println(msg)
  } else {
      fmt.Println(msg)
  }
  ```
- ## Golang中的switch语句
  - 在Golang中，并不需要为switch中的每个case手动添加break，Golang的switch只会执行第一个匹配的case后的语句
  - 在Golang中，case条件后跟的不一定非要是常量，也可以是变量或者是函数调用
  - 在Golang中，case后跟的也不一定是整形，而可以是其他类型
  ```Golang
  func f() {
      return 0
  }

  // ...
  a:=0
  switch a {
      case f():
        fmt.Println("matched")
      default:
        fmt.Println("missed")
  }
  ```
  - 在Golang中，switch语句可以不跟条件表达式，其默认和switch true相同：
  ```Golang
  switch{
      case condition-1:
        // ...
      case condition-2:
        // ...
      default:
        // ...
  }
  和
  if condition-1 {
      //...
  }else if condition-2 {
      //...
  }else {
      //...
  }
  相同
  ```
- ## Golang中的defer
  - 在Golang中，可以用defer关键字将函数的执行推迟到当前defer语句所处的函数执行完成之后
  - 在Golang中，defer关键字修饰的函数调用，其参数是立即被计算的，但是函数调用会被推迟到外围函数执行结束之后
  - Golang中defer推迟的函数调用将会被压入到栈结构中，如果在同一个函数中存在多个defer调用，那么在函数执行结束后，后被压入栈结构的defer调用会先被调用
  ```Golang
  func f() {
      fmt.Println("function begin")

      defer fmt.Println(1)
      defer fmt.Println(2)
      defer fmt.Println(3)

      fmt.Println("function end")
  }
  // 在调用f()后，其输出会按如下顺序
  //    function begin
  //    funtion end
  //    3
  //    2
  //    1
  ```
- ## Golang中的指针
  - Golang中指针的声明
  ```Golang
  var p *int
  ```
  - Golang中的取址操作
  ```Golang
  var (
      i int = 1
      p *int = &i
  )
  ```
  - Golang中的解引用操作
  ```Golang
  var (
      i int = 1
      p *int = &i
  )
  fmt.Println(*p)
  *p=2
  fmt.Println(*p)
  ```
  - Golang中并不存在指针的算数，如通过+或者-一个指针将指针地址向前、向后推移
- ## Golang中的struct
  - Golang中struct的定义
  ```Golang
  type Node struct {
      val int
      next *int
  }

  fmt.Println(Node{1,2})
  ```
  - Golang结构体中域的引用
  ```Golang
  type Result struct {
      retCode int
      msg string
  }

  var result Result=Result{200 "response success"}
  fmt.Println(result.retCode,result.msg)
  ```
  - 通过指针访问结构体中的内容
  ```Golang
  node := Node{0,nil}
  // 要想通过指针访问结构体中的域，可以如下所示
  fmt.Println((*node).val)
  // 也可以直接在指针后调用.
  fmt.Println(node.val)
  ```
  - 结构体字面量
  ```Golang
  // 可以通过为每个域按顺序指定值
  var result Result=Result{
      200,
      "请求成功"
  }
  // 可以通过filedName:value来为域的子集指定值
  //    其他未指定的值为默认值
  var result Result=Result{
      // retCode未指定默认为0
      msg:"请求成功"
  }
  // 可以不为域指定值，域中所有值都为默认值
  result := Result{}
  ```
- ## Golang中的数组
  - Golang中数组类型为[n]T，代表长度为n的T类型的数组
  - Golang中的数组无法被扩容
  - slice：slice中包含数组中元素的子集，可以作为数组的一个动态视图，通过slice比数组使用的更为广泛
  - slice的类型为[]T，T是元素的类型
  - slice本身并不存储任何元素，其只是一个针对数组中一定范围内元素的引用
  - 在slice中对元素进行的更改在数组中也可见，在数组中对元素进行的更改同样在slice中可见
  - 可以对slice创建slice
  - 在创建slice之前无需先创建数组，可以直接创建slice，创建slice的同时会创建数组并且让slice引用创建的数组
  ```Golang
  // 声明一个数组
  arr := [5]int{1,2,3,4,5}
  // 对数组进行切片，获取数组的slice
  //    其中以array[lo : hi]形式来切片
  //    slice中包含下标为lo的元素
  //    但是slice不包含下标为hi的元素
  slice := arr[1:3]

  // 直接创建slice
  slice_dir=[]int{1,2,3,4,5}
  ```
  - 在进行slice操作时，可以对下标的下界和上界进行省略，在省略下界时，默认下界为0,在省略上界时，默认值为数组长度
  ```Golang
  arr := []int{1,2,3,4,5,6,7}
  fmt.Println(arr[:4][1:]) // 返回[2,3,4]
  ```
  - slice的length和capacity
  ```Golang
  // slice的length是指slice中元素的个数
  // slice的capacity是值从slice中第一个元素开始算起，到
  //    array中的最后一个元素，一共有多少个元素
  arr := []int{1,2,3,4,5,6,7,8,9}
        
  slice := arr[1:4]                     
  fmt.Println(len(arr),cap(arr))
  // 输出length为3
  // 输出cap为8
  ```
  - slice可以对其进行重新切片，虽然无法通过数组下标访问位于slice范围外的元素，但是可以通过重新切片来将slice范围扩大到数组范围外
  ```Golang
  arr := []{1,2,3,4,5,6,7,8,9}

  // 切片[2,3,4]
  arr = arr[1:4]
  // 重新切片为[2,3,4,5]
  arr = arr[:4]
  ```
  - slice变量的默认值是nil，其cap和len都是0,并且没有底层的数组
  - Golang中创建动态大小的数组
  ```Golang
  // make函数类似于c++中的new T[n],返回一个slice
  // 返回数组中元素值都是默认值

  // 创建一个slice，并且指定len和cap为相同值
  // 其中len为5,cap为5
  arr := make([]int,5)

  // 创建一个slice，并分别指定len和cap
  // 其中，len为5,cap为10
  arr := make([]int,5,10)
  ```
  - Slice中可以包含slice，类似于多维数组
  ```Golang
  slice ：= [][]string {
      []string {"touma","kazusa"},
      []string {"kitahara","haruki"}
  }
  ```
  - Golang中向slice添加元素
    - 可以通过append方法向slice中添加元素，如果slice中位于len后的cap可以容纳添加的所有元素，就在当前数组中修改位于slice之后的元素
    - 如果当前slice的cap无法容纳append中所有的元素，会重新创建一个数组，拷贝当前slice中所有的元素，在添加元素后返回一个指向新分配数组的slice
  ```Golang
  arr := []int{1,2,3,4}
  t := append(arr[:3],-1,2)
  fmt.Println(arr,t)
  // 输出结果如下
  // arr [1,2,3,4]
  // t   [1,2,3,-1,2]
  // arr和t并不指向同一个底层数组
  ```
- ## Range的形式迭代slice
  - 可以通过range的形式对slice进行迭代：
  ```Golang
  // 其中i是当前迭代元素的下标
  // v是当前迭代元素值的拷贝
  for i,v := range slice {
      fmt.Println(i,v)
  }
  ```
  - 在只想在range中访问index或是value时，可以使用_作为占位符
  ```Golang
  // 省略value
  for i,_ := range slice {
      fmt.Println(i)
  }

  // 省略value时，_也可以省略
  for i := range slice {
      fmt.Println(i)
  }

  // 省略index
  for _,v := range slice {
      fmt.Println(v)
  }
  ```
- ## Golang中的map
  - 在Golang中，map的默认值是nil，既没有键值对，也无法加入键值对
  - 可以通过make函数返回指定类型的map，返回的map已经被初始化并且准备好被使用
  ```Golang
  m := make(map[string]string)
  m["shiro"]="kana"
  m["black"]="kazusa"
  fmt.Println(m)
  ```
  - map字面量
  ```Golang
  m ：= map[string]string{
      "shiro":"kana",
      "black":"kazusa",
  }
  fmt.Println(m)
  ```
  - map中如果字面量中元素的值是struct类型，那么可以省略值类型中的类型名称
  ```Golang
  type Point struct {
      X float64
      Y float64
  }

  m := map[string]Point{
      "point-a":{1,1},
      "point-b":{2,2}
  }

  fmt.Println(m)
  ```
  - 对map进行操作
  ```Golang
  // 插入或者更新map中元素
  m["touma"]="kazusa"

  // 获取map中元素,如果map中不存在该元素，返回该元素类型的默认值
  elem = m["touma"]

  // 判断map中是否存在key为指定值的键值对
  elem,ok := m["shiro"]
  // 如果不存在，elem为默认值，ok为false

  // 从map中删除key为指定值的元素
  delete(m,key)
  ```
- ## Golang中函数的值传递
  - Golang中函数可以作为值传递给变量或者其他函数的参数，也可以作为其他函数的返回值
  ```Golang
  func operateOnArgs(f func (float64,float64) float64,a,b float64) float64 {
      return f(a,b)
  }

  var f=func(a,b float64) float64{
      return math.Sqrt(a*a+b*b)
  }
  fmt.Println(operateOnArgs(f,3,4))
  ```
  - Golang中函数的闭包
    - Golang中支持函数的闭包，通过将一个函数作为函数的返回值，返回的函数可以访问外层函数中定义的局部变量，位于外层函数之外只能通过返回的函数来访问外层函数内部的局部变量，如此保证了局部变量的安全性
    ```Golang
    func adder() func(int) int {
        sum := 0
        return func(a int) int {
            sum+=a
            return sum
        }

    }
    ```
  


