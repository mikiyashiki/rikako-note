# JavaScript基础
- ## js特性：
  - js是一门动态语言，包含类型、运算符、标准内置对象和方法
  - js支持函数是编程
  - js通过原型链来实现面向对象编程
- ## js类型
  - js中包括如下类型：  
    - Number：数字
    - String：字符串
    - Boolean：布尔
    - Function：函数
    - Object：对象
    - Symbol：符号
    - 其他类型：
      - undefined
      - null
      - Array
      - Date
      - RegExp
- ## js变量的声明
  - js可通过如下关键字来声明变量：
    - let：
      - 块级作用域内有效，只能在声明该变量的块中使用
      - 在let声明变量之后，变量只能在声明之后被使用
    - const：
      - 声明一个不可变的变量，该变量在定义域中总是可见的
      - const变量也只能在块定义域中被使用，在声明块之外无法访问
    - var：
      - 通过var关键字声明变量之后，变量在整个函数的范围内都是可见的
      - 在定义var变量的方法之外，均无法访问var变量
      - 但是在声明var变量的方法中，即使在声明var变量的语句之前，也可以使用var变量，变量值为undefined
  - js变量作用域特性：
    - 在js语言中，原本是没有块作用域的，变量只有函数范围的作用域。但是，通过引入const和let关键字，js支持声明块作用域的变量。
- ## js运算符
  - ==运算符和===运算符
    - ==运算符具有类型自适应的功能
      ```java
      # 对于==运算符，如果运算符两边的类型不一致的话，
      #   会尝试进行类型转换并比较
      123==“123” true
      1==true true
      ```
    - ===相对于==，比较前并不会进行强制类型转换
      ```java
      1===‘1’ false
      1===true false
      ```
    - 相对应的，对于比较不相等的操作，也存在!=和!==操作，其区别类似于==和===的区别
- ## js流程控制
  - js中for循环可以分为如下两种
    - for in循环：
      - 对于for in循环，会遍历索引项（对数组来说是序号，对map来说是key，set为空）
    - for of循环：
      - 对于for of循环，会遍历值（value）而不是键（key）
  - js中的switch流：
    - 相比于c中的switch只支持数字，js中的switch既支持数字又支持字符串，switch和case之间是通过===运算符来判断是否严格相等的
- ## js对象
  - js对象类似与map结构，由key和value组成的键值对构成
  - js对象的创建有如下两种方式
    - 字面量创建
    ```javascript
    let obj={
      name:"touma",
      id:"A001",
      husband:{
        name:"haruki",
        id:"A002"
      }
    }
    ```
    - 通过对象原型来创建对象
    ```javascript
    function Person(name,id) {
      this.name=name
      this.id=id
    }

    let person=new Person("touma","A001")
    ```
  - 访问对象元素的方式
  ```javascript
  let person={
    name:"touma",
    id:"A001"
  }
  // 通过.来访问
  cosnole.log(person.name)
  // 通过[]来访问
  console.log(person["name"])
  ```
- ## js数组
  - js中允许通过下标在超过当前数组长度的位置插入元素，其中，中间未插入元素的部分元素值均为undefined
  ```javascript
  let arr=[1,2];
  arr[100]=101;
  // 其中，下标位于2～99之间的元素均为undefined，数组长度为101
  ```
  - js中数组可以通过forEach方法对数组中每个值执行调用
  ```javascript
  let arr=[1,2];
  // 将数组中的每个元素平方
  arr.forEach((value,index,arr)=>{
    arr[index]=value*value;
  });
  ```
- ## js中的函数
  - 函数在调用结束之后都会返回一个值，如果函数在结束时没有return语句，或是return语句后没有跟随值，都会返回一个undefined
  - 在js函数中，可以通过展开运算符...来接受任意个数的参数，并且，对于数组，...也可以将其展开为被逗号分隔的参数列表
  ```javascript
  let arr = [1, 2, 3];

  function sum(...arr) {
      let result = 0;
      for (let ele of arr) {
          result+=ele;
      }
      return result;
  }

  console.log(sum(...arr));
  ```
- ## js中的嵌套函数
  - 如果在js中，一个函数依赖与其他函数，但是依赖的其他函数并不会在其他地方被使用，那么可以将依赖的函数放在函数内部
  ```javascript
  function outer_func() {
    function inner_func() {
      // 除了被outer_func调用外不会在其他地方被调用
    }
  }
  ```
  - 通过嵌套函数，可以有效的减少全局作用域中的函数名称，可以防止污染全局作用域的命名
- ## js中的闭包
  - 每当js中调用一次函数时，都会创建一个作用域对象，用来保存在函数中创建的局部变量。
  - 当返回值为函数时，返回的函数中将会指向先前被创建的作用域对象，这样作用域对象在函数结束后就不会被回收
  - 由于闭包可以保存状态，并且对外部隐藏状态，故而闭包可以代替对象来使用。闭包通过向外部返回一个操作内部局部变量的函数，保证位于内部的局部变量只能通过返回的函数来访问，保证了变量的安全
- ## js继承和原型链
  - 和面向对象的编程语言不同，js的继承是通过原型来实现的。每个对象都有一个原型对象，以原型为模板，从原型中继承方法和属性。原型对象也拥有原型，一层一层，以此类推，构成了原型链。
  - 每一个对象都有内置的属性，其中，可以通过内置的__proto__属性来访问一个对象的prototype对象
  - 原型链中属性的查找规则：
    - 如果在一个对象中想要查找的属性不存在，那么将会在当前对象的prototype对象中查找属性
    - 如果prototype对象中属性仍然找不到，那么就在prototype对象的prototype对象中查找属性
    - 一直向前回溯，直到属性被找到或者回溯到原型链尾部
  - 原型链结构：
    - 对于所有的对象，默认__proto__指向的原型对象都是Object.prototype
    - Obejct.prototype是位于原型链尾端的对象，Object.prototype的原型对象是null
  - 为对象指定原型对象：
    - 通过Object.create函数指定原型对象,该方法会创建一个新对象并为其指明原型对象
    ```javascript
    let proto_obj={
      name: "kazusa",
      greeting() {
        console.log("hello, "+this.name);
      }
    };
    let child_obj=Object.create(proto_obj);
    child_obj.greeting();
    ```
  - 通过构造函数
    - 在js中，所有函数都有一个prototype属性，当该函数被作为构造函数调用时，构造函数创建的所有新对象的__proto__属性都会被设置为该函数的prototype属性
    - 在将构造函数的protype属性设置为制定的对象后，prototype对象中内置属性constructor发生了变化，需要重新将原型对象的constructor制定为构造函数
- ## 在js中实现类的继承
  - js中，有如下关键字可以简化类的继承，其底层仍然使用原型链来实现类的继承关系
    - class：用于类的声明
    - extends：用于类的继承
    - constructor：用于声明构造函数
    - super()：调用父类构造函数来实现子类构造函数
  - js中，可以通过#字符来将变量或者方法标明为类私有的
  - js中继承实现
  ```javascript
  class People {
      #name

      constructor(name) {
          this.#name = name
      }

      greeting() {
          console.log("hello, i am " + this.#name)
      }
  }


  class Student extends People {
      #grade

      constructor(name, grade) {
          super(name)
          this.#grade = grade
      }

      greeting() {
          console.log("hi, fucking bitch, miss your dad?")
      }
  }

  let p=new Student("touma",6)
  console.log(Object.getPrototypeOf(p))
  ```
  - js通过原型链实现继承的细节：
    - js可以首先为父类创建构造函数
    - js在子类的构造函数中，可以先调用ParentConstructor.call（this,args)方法来对子类的对象添加父类的成员变量
    - 在完成子类对象成员变量的赋值后，可以通过Object.create创建一个以ParentConstructor.prototype为__proto__的新对象，并且将ChildConstructor.prototype设置为新对象
    - 最后，将新对象的constructor属性设置为ChildConstructor