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
- ## Golang中的方法
  - 虽然Golang中并不像其他语言一样存在类（class），但是我们可以在Golang中为类型（type）定义方法
  - 在Golang中，为类型（type）定义方法是定义一个接受特定类型参数的函数
  ```Golang
  type Point struct {
    X,Y float64
  }

  // 为Point类型定义方法
  func (p Point) Abs() float64 {
    return math.Sqrt(p.X*p.X+p.Y*p.Y)
  }

  // 可以通过如下形式调用为类型定义的方法
  p := Point{3,4}
  fmt.Println(p.Abs())
  ```
  - 在Golang中，不仅可以为struct类型指定方法，而且可以为基本类型指定方法（例如type MyFloat float64后，为MyFloat类型指定方法）
  - 在Golang中，只能够为位于同一个包中的类型指定方法，无法为位于其他包中的类型指定方法
  ```Golang
  type MyFloat float64

  func (f MyFloat) fuck() {
    fmt.Println("fucking float64")
  }
  ```
  - 在Golang中，除了可以为类型指定方法外，还可以为类型的指针指定方法，此时特殊参数接受一个指针，可以通过指针修改传输类型的值
  ```Golang
  type Point struct {
    X,Y float64
  }

  // 定义函数接受一个Point类型的指针
  func (p *Point) modify() {
    p.X *= 10
    p.Y *= 10
  }

  // 调用时无需先将类型对象转化为指针，可以直接在对象上调用
  p := Point{3,4}
  p.modify()
  // 调用后p为{30,40}

  // 接受一个Point类型的拷贝
  func (p Point) modifyCopy() {
    p.X *= 10
    p.Y *= 10
  }

  // 调用时传入函数的是对象的一个拷贝，不会修改原先对象的值
  p := Point{3,4}
  p.modifyCopy()
  // 调用后仍然为{3,4}，修改的只是拷贝对象的值
  ```
  - 在Golang中，普通函数参数，必须向接受指针的形参中传入指针，向接受值的形参中传入值；但是在接受特殊参数的位置，如果想要接受的是值类型，那么传入指针和传入值均可；如果想在特殊参数位置接受指针类型，那么传入指针和传入值类型也均可
  ```Golang
  func (p *Point) modify() {
    // func body
  }

  // 对于如下调用形式均可
  p := Point{3,4}
  
  p.modify() // 可行的调用方式
  (&p).modify() //可行的调用方式

  func (p Point) modifyCopy() {
    // func
  }

  p := Point{3,4}
  // 对接受值的特殊参数，下列调用方式也均可
  p.modifyCopy() // 可行
  (&p).modifyCopy() //可行
  ```
- ## Golang中的接口
  - 在Golang中，interface中定义了一系列的方法签名，对于实现了interface中定义所有方法签名的类型，其都可以被赋值给interface类型
  - 如果对一个类型的指针实现了interface接口，那么只能将该类型的指针赋值给interface变量，无法将该类型赋值给interface变量
  ```Golang
  type Car interface {
    drive()
  }

  type AE86 struct {
  }

  // 为AE86类型的指针实现接口中drive方法
  func (car *AE86) drive() {

  }

  // 此时，可以将*AE86类型赋值给Car，但是无法将AE86赋值给Car
  var car Car
  car = AE86{} // 失败
  car = &AE86{} // 成功
  ```
  - 接口中保存的信息通常可以看做一个二元组：（具体的值，值的类型）
  ```Golang
  // 接口中保存的值如果是nil，但是其类型已经确定，那么接口的值就不为nil
  var car Car
  car=(*AE86)(nil)
  fmt.Println(car==nil) // 输出为false
  // 此时，虽然car仍然不指向具体的对象，但是其指向的类型确定为Car，故而其不为nil
  // 在明确接口所指向的具体类型后，即使其值仍然为nil，但仍可以调用类型的具体方法
  car.drive() // 调用成功
  // 虽然car作为interface值时car==nil并不成立
  // 但是调用car.drive()时，向drive函数中传递的*AE86类型的特殊参数仍然是nil
  // 故而在func (car *AE86) drive() {}函数的实现中，需要显式定义空指针判断
  func (car *AE86) drive() {
    if car==nil {
      // operations if nil...
    }
    // operations if non-nil...
  }
  ```
  - 如果在接口中，及没有保存值，也没有保存类型，那么接口值为nil，在为nil的接口值上调用方法会引发空指针异常
  ```Golang
  var car Car
  // 此时car接口中既没有值信息，又没有类型信息
  fmt.Println(car==nil) // 输出结果为true

  // 抛出空指针异常
  // 由于car接口中并没有保存当前的值和类型信息
  // 故而不知道应该对那个类型的drive方法进行调用
  car.drvie()
  ```
  - 空接口类型interface{}
    - 由于interface{}接口中没有定义任何方法，故而任何类型都实现了interface{}中所有的方法，interface{}接口可以持有仍和类型的值
    - 通常，可以在函数形参中接受interface{}类型的变量，代表接受任何类型的值
  ```Golang
  // 定义新的类型any，其相当于interface{}，可接受任何类型的值
  type any interface{}
  ```
   - Golang中的类型断言
     - 在Golang中，接口可以在运行时持有不同类型的值，类似与面向对象中的多态
     - 通过类型断言，可以在运行时动态的判断interface持有值的类型
  ```Golang
  // 运行时动态判断接口中持有值的类型
  var car Car=(*AE86)(nil)
  // 断言判断
  // 1.如果interface中持有的值不是指定类型，抛出panic
  val := car.(*FD) // 此时会抛出panic，终止程序

  // 2.判断interface中持有的值是否是制定类型，如果不是，不抛出panic继续运行
  val,ok := car.(*FD)
  // 如果是，ok为true，如果不是，ok为false
  if ok {
    // val为持有的值
  } else {
    // val为该*FD类型的默认值，指针类型默认值为nil
  }
  ```
  - Golang中根据接口持有类型来进行switch
  ```Golang
  // 进行type switch
  var car Car=&AE86{}

  switch v := car.(type) {
    case *AE86:
      // 如果car接口持有的类型是*AE86，则v的类型是*AE86，值为car持有的值
    case *FD:
      // 如果car接口持有的类型是*FD，那么v的类型是*FD，值为car持有的值
    default：
      // 如果car接口持有的类型不是上述类型，那么v的类型仍然是接口类型，值为car持有的值
  }
  ```
  - Golang中的Stringer接口
    - Golang中通常通过Stringer中的String()方法来返回一个描述自身的字符串，而fmt包在打印信息时通常通过调用该接口的String方法来实现的
  ```Golang
  type Waifu struct{
    firstName,lastName string
  }

  func (waifu Waifu) String() string {
    return "my waifu: "+waifu.firstName+" "+waifu.lastName
  }

  fmt.Println(Waifu{"touma","kazusa"})
  ```
- ## Golang中的错误处理
  - Golang中，通常在方法执行时，都会返回一个error值，调用方通过检查error返回值是否为nil来判断执行是否出错
  ```Golang
  i,err := strconv.Atoi("1")
  if err==nil {
		// 转换成功
		fmt.Println(i)
	} else {
    // 转换失败
		fmt.Println(err)
	}
  ```
  - 在Golang中，通常通过error接口来获取失败的错误信息
  ```Golang
  type error interface {
    Error() string
  }
  ```
- ## Golang中的字节流
  - Golang中，通过io.Reader接口来完成对文件的读取
  ```Golang
  type Reader interface {
    // Reader接口中含有一个read方法
    // read方法接受一个字节slice作为参数
    // 并且，其会将读取的字节填充到slice中
    // read方法的返回值为n和err
    // n为读取的字节数
    // err为返回的异常信息，如果err返回的值是io.EOF，则代表读取到字节流的末尾
    Read(b []byte) (n int,err error)
  }
  ```
- ## Golang中的泛型
  - Golang中，支持泛型函数，可以为函数指定一个类型参数，指定函数接受参数的类型
  ```Golang
  // 定义泛型参数，添加对类型的约束条件，要求其可以比较
  func firstIndex[T comparable](arr []T,t T) int {
    for i,v := range arr {
      if v==t {
        return i
      }
    }
    return -1
  }

  fmt.Println(firstIndex[int]([]int{1,3,2,5,4},5))// 输出3
  ```
  - Golang中，除了支持泛型函数，还支持泛型struct
  ```Golang
  // 通过泛型struct定义单链表节点
  type Node[T any] struct {
    val T
    next *Node[T]
  }
  ```
- ## Golang中的并发
  - Goroutine：
    - Goroutine是由Go的运行时环境所管理的轻量级线程
      - Goroutine运行在相同的地址空间中，故而在Goroutinue在对地址中资源进行访问时，需要对其进行同步
    - Goroutine的开启：
    ```Golang
    // 开启一个新的Goroutine
    // 其中，f、x、y、z的计算发生在当前的Goroutine中
    //  但是，f(x,y,z)的执行发生在新创建的Goroutine中
    go f(x,y,z)
    ```
  - Channel：
    - Channel是一个类型化的管道，通过<-操作符，可以从Channel中接收数据或向Channel发送数据
    - Channel的创建可以通过make函数来实现
    ```Golang
    // 创建channel
    ch := make(chan int)

    // 与channel的数据传输
    ch<-v // 将v中的数据发送到channel管道
    v := <-ch // 从channel中读取数据，并且将其赋值给v
    ```
    - Channel中发送和接收的同步
      - 默认情况下(没有缓冲时），向channel发送数据时，需要确认接受数据的一方已经准备好
      - 在从channel接受数据时，需要确认发送数据的一端已经准备好
      - 通过上述条件，可以让Goroutine在没有显式锁和条件变量的情况下完成同步
    - 在创建channel时，可以为channel指定一个缓冲区大小
      - 当缓冲区中的数据满之后，向缓冲区填充数据的Goroutine将会被阻塞
      - 当缓冲区中的数据为空时，从缓冲区读取数据的Goroutine将会被阻塞
    ```Golang
    // 如果在同一个Goroutine中，多次向channel中加入数据导致缓冲区被填满
    // 并且没有其他Goroutine来取出数据，那么当前添加数据的Goroutine将会
    // 被一直阻塞，从而产生死锁
    func main {
      ch := make(chan int,2)
      ch <- 1
      ch <- 2
      ch <- 3 // 此时缓冲区已满，会阻塞当前Golang，造成死锁
    }
    ```
    - Channel的关闭和range迭代
      - Channel的发送方可以调用close函数对channel进行关闭
      - 向已经关闭的channel发送数据，会抛出panic
      - 从已经关闭的channel中读取数据时，第二个参数ok返回为false
      - 在对panic进行读取操作时，也可以用第二个参数来读取channel是否被关闭
    ```Golang
    ch := make(chan int,2)
    ch <- 1
    ch <- 2

    close(ch)

    // 可以通过读取channel时的第二个参数来读取channel是否被关闭
    i,ok := <-ch
    // 此时，ok代表是否ch已经关闭，i代表读取的值
    fmt.Println(i,ok) // 1 true
    i,ok = <-ch
    fmt.Println(i,ok) // 2 true
    i,ok =<-ch
    fmt.Println(i,ok) // 0 false

    // 可以对channel中的所有数据进行range循环
    // 该循环会遍历channel中所有元素，直到channel被关闭
    for i := range ch {
      fmt.Println(i)
    }
    ```
  - Golang中的select块
    - Golang中select块可含有多个被阻塞的通信操作，当任意一个通信操作结束时，select结束阻塞并且执行其通信操作。
    - 如果在select块中有多个通信操作都处于就绪状态，其随机选择一个执行
    - 如果在select块中存在default，则在其他case均未处于就绪状态时default条件下被执行
  ```Golang
  select {
    case v:=<-ch:
      fmt.Println(v)
    case <-quit
      return
    default:
      fmt.Println("waiting...")
      time.Sleep(time.Second * 1)
  }
  ```
 - Golang中的互斥锁
   - Golang中为了保证临界区共享资源的安全，可以通过sync.Mutex来保证多个Goroutine访问临界资源时的并发安全
   - Mutex有Lock和Unlock方法，用于加锁和释放锁
   - 为了保证解锁的方法一定被执行，可以指定Unlock方法为defer，此时即使发生了panic，也会保证defer的Unlock方法一定被执行，锁资源被释放
    ```Golang
    mut := sync.Mutex{}
    cur := 0
    go func() {
      for cur<9 {
        mut.Lock()
        if cur<9 && cur%3 == 0 {
          fmt.Println("Goroutine 1")
          cur++
        }
        mut.Unlock()
      }
    }()
    go func() {
      for cur<9 {
        mut.Lock()
        if cur<9 && cur%3 == 1 {
          fmt.Println("Goroutine 2")
          cur++
        }
        mut.Unlock()
      }
    }()
    go func() {
      for cur<9 {
        mut.Lock()
        if cur<9 && cur%3 == 2 {
          fmt.Println("Goroutine 3")
          cur++
        }
        mut.Unlock()
      }
    }()
    // 输出会显示如下
    // 1
    // 2
    // 3
    // 1
    // 2
    // 3
    ```