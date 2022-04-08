# dart语法
* ## dart语法中需要注意的事项
  * print方法传入对象时默认会将对象转化为字符串，会通过toString方法来转化。默认情况下toString方法未被重写，此时会返回”Instance of 'ClassName'“
  * 字符串可以通过 " ${varname}" 来将变量值填充到字符串中
  * @override会告知编译器检查是否方法被覆盖
  * 若想把变量声明为私有的，需要在变量名之前加上单下划线_，加上下划线前缀后该变量只能在当前文件的可见，在其他文件中是不可见的（dart中不存在类范围可见）
  * dart中的变量是不能为空的，变量必须要经过初始化。如果变量可以为空，要在变量类型后面加上?
  * 默认情况下，dart为公共的实例变量提供了隐式的getter和setter，不需要手动为变量指定getter和setter，除非想要将变量标明为只读或者只写的
  * final和const都表示一个变量在赋值之后不能被修改，但是const代表这个变量是编译时常量，而final修饰的变量可以用变量为其赋值，但是赋值之后final修饰的变量不可变
  * const除了可以用来修饰变量以外，还能够用来修饰变量值。可以为list等类型的值指定一个const，如const []，此时该值是无法修改的，无法向list中添加元素、
  * 可以通过get和set方法来为字段指定getter和setter，以此将字段表示为只读或者只写的
  * dart并不支持重载，但是dart可以设置可选参数，可选的命名参数通过{}来包围，并且命名参数可以通过=来赋默认值
  * 可以通过factory关键字来为类型创建工厂构造方法
  * dart中可以通过implements关键字来实现类之间的继承关系。每个类都定义了一个接口，如果一个类被其他类implements，那么子类会继承父类的接口，但是子类并不会继承父类的实现，需要手动实现父类中的方法
  * dart支持函数式编程，函数能作为参数传递给方法，并且函数能赋值给变量
* ## dart中的异步操作
  * dart中可以通过Future来保存异步操作的结果，Future有两个状态，已完成和未完成
  * 当调用一个异步方法时，该方法会返回一个Future<T>对象，并且该Future对象的状态为未完成
  * 可以通过async关键字和await关键字来定义异步方法并且使用它们的返回值，await只能够在async方法中使用
* ## dart中的类型
  * dart中所有的变量引用的都是对象，对象都是一个真实类的实例。除了null以外，所有的类都继承自object。
  * dynamic、Object、var区别：
    * Object：可以将任何对象赋值给Object引用，所有类都继承自Object
    * var：编译器会动态推断var变量所引用的对象类型，当给var变量赋一种类型的值后，var变量就被自动推断为这种类型，之后无法将其他类型的值赋值给var变量
    * dynamic：将类型检查延迟到运行时，但是这样可能会造成语法检查失效的情况，如果对dynamic变量引用的对象调用其中不存在的方法，这样编辑代码时也不会报错，而是直到运行时才抛出异常
  * late修饰符：
    * 对于局部变量，dart通常可以判断不可为空的变量何时被初始化，故而其可以在声明时不初始化，而是后续初始化
    * 但是对于全局变量和实例变量，dart无法判断其何时被初始化，故而dart不会对其进行判断，在声明时必须被初始化
      * 如果确定全局变量或实例变量后续会被初始化，那么可以为其指明late修饰符，表明其会序会被初始化，编译器会取消报错
      * 如果被late修饰的变量在声明时初始化，那么其初始化过程只会在变量第一次被使用时调用，如果一个声明为late的变量没有被调用，那么其初始化过程也不会被调用
    * dart中类型：
      * Numbers：
        * int：整数类型
        * double：浮点数类型
        * num：如果变量被声明为num类型，那么其既可以为int，也可以为double
        * 字符串和数字之间的转化可以通过如下安徽念书来实现：
          * int.parse
          * double.parse
          * intObject.toString
          * doubleObject.toString
      * Strings:
        * dart中String类似于java中的String，如果想要对字符串进行修改，可以使用StringBuffer
        * 对于字符串，如果在字符串之前加上r，表示该字符串是raw的，该字符串中的内容将不会被转义，如\n和${}等字符串中内容都不会被转义
      * 布尔
      * Lists：
        * 对List类型的对象，可以采用扩展操作符...或者...?来对list对象中的元素进行拓展
        * 可以通过for和if语法来对List集合中插入元素
      * Sets
      * Maps
    * 函数：
      * dart中函数可以可以通过{}来设置可选参数，如果可选参数列表中某参数是必须的，可以在该参数之前加上required关键字
      * dart中可以通过[]来标明可选的位置参数，通常将[]放置到最后
      * 词法闭包：
        * 定义在方法内部的方法可以访问其外部的变量，并且持有其状态
        * 通过闭包，可以隐藏变量，防止其变量被其他方法直接访问，而是通过调用返回的方法来访问和修改对象
    * 异常：
      * dart中异常可以通过on或者catch来捕获：
        * on XXXException会捕获特定类型的异常，比如on FormatException {}
        * catch捕获非特定类型的异常
      * 通过rethrow关键字可以将捕获的异常重新抛出
  * ## dart中的类机制
    * 对于dart中的类型，除了null之外，所有的类都继承自Object类
    * 如果子类要调用父类的构造方法，需要在初始化列表中进行调用，类似于c++语法
    * 可以在初始化列表中指定重定向构造函数，此时可以通过this(params...)方法来调用其他的构造函数
    * 常量构造函数：
      * 如果生成类的对象都是不可变的，可以在构造函数之前加上const来标识其为常量构造函数
      * 工厂构造函数：
        * 通过factory关键字可以指定工厂构造函数
    * 运算符重写：
      * 可以通过operator关键字来对类型的操作符进行重写
    * getter/setter方法：
      * 可以通过get、set关键字来添加getter和setter方法
    * 类接口：
      * 每个类都隐式定义了一个接口并且实现了该接口，该隐式接口包含了类所有的实例成员并且包含这个类所实现的其他接口。如果一个想让类B调用类A的API并且不想继承类A的实现，则可以让类B实现类A的接口
    * 扩展类：
      * 可以通过extends关键字来继承父类并且可以覆盖其实现
  * ## dart中的泛型机制
    * 通过<>可以指定泛型
    * 可以通过extends关键字限定泛型的范围，例如List<T extends Object>将类型T限定为Object类的子类