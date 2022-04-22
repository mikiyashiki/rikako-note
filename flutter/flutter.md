# Flutter
* ## Flutter结构：
  * Flutter的UI界面由Widget来构成，每当构成界面的Widget状态发生改变，那么其将会对Widget构成的UI进行重新渲染
  * 为了创建一个Flutter应用，仅需调用runApp方法并且向其中传入一个Widget即可，传入的Widget将会成为Widget树中的根节点
* ## Flutter中的基础Widget
  * ### Text：Text Widget用来表示一组具有同一样式的文本
  * ### Row：用来构造水平布局
    * 要想使用水平布局，可以向Row的children属性中传入一个Widget的List，如果想要子Widget扩展去尽可能的填充可以获取的垂直控件，可以将子Widget用Expanded包裹
  * ### Column： 类似于Row，用来构建垂直布局
  * ### Stack布局：Stack布局可以将children属性中的Widget集合重叠起来，默认左上角对齐。Stack中的Wdiget按先后顺序进行绘制，位于第一的Wiget会被绘制到最底层，后面的元素依次覆盖前一个元素。
    * Stack中可以由Positioned元素和non-Positioned元素，Positioned元素由Positioned Widget包裹，可以指定其在Stack中相对Stack的位置，而不是默认左上角对齐
  * ### Container：创建一个可见的矩形区域，可以通过BoxDecoration来指定Container的边框样式、背景颜色等
  * ### 可以通过MaterialApp和Scaffold等组件来构建Material Design的APP
* ## 处理用户的手势
  * 通过用GestureDetector包裹Widget可以检测用户的手势，并为onTap等手势创建回调函数
* ## StatefulWidget
  * 相比较于StatelessWidget，StatefulWidget可以维护状态信息
  * StatefulWidget的使用：
    * StatefulWidget子类通过覆盖createState方法可以维护一个State对象来存储状态信息
    * 在State对象中，
      * 通过覆盖State对象的build方法，返回一个Widget对象来显示StatefulWidget的UI
      * 每次对State中维护的状态进行修改时，都要调用setState方法，并且传入一个函数
      * 每次调用setState方法时，都会重新调用build方法来对StatefulWidget的UI进行重新渲染
  * StatefulWdiget管理状态的三种方式：
    *  widget管理自己的状态
       *  此时widget为stateful的，并在state中存储状态信息
    * 由父widget管理状态
      * 此时widget是stateless的，而父widget是stateful的，当前无状态widget只负责显示信息，而父widget则会通过构造函数向stateless widget传递存储在父widget中的状态信息，父widget也会传递一个回调用来对存储在父widget中的数据进行修改
    * 一部分状态存储在父widget中，一部分状态存储在自身的widget中
* ## Flutter中的布局
  * Row和Column：通常用于创建行和列的布局
    * mainAxisAlignment/crossAxisAlignment：用于控制主轴和交叉轴如何对其元素
    * 当Widget太大而超出布局时，可以通过为Widget外层套一个Expand来让其适应行或者列布局
    * 如果在行或者列布局中，想要一个widget的弹性布局空间是其他兄弟widget的2倍，可以设置Expand的flex属性为2。flex属性默认值是1.
    * 默认情况下，行或列布局会沿着主轴尽量的占据空间。如果要将子项目尽量贴合在一起，可以使用mainAxisSize属性，将其设置为mainAxisSize.min
  * 标准widget
    * Container：类似于html中的div，可以为widget增加padding、margin、borders、background color或者其他的装饰
    * GridView：将widget作为二位列表显示，有两种方式来创建GridView
      * GridView.extent：通过指定单元格的最大宽度
      * GridView.count：指定列的数量
    * ListView：当内容过多时会自动的支持滚动
    * Stack：Stack布局会将children中位于后面的Widget覆盖到位于前面的Widget之上
  * Material Widget
    * Card：通过将Widget设置为Card的child，可以添加圆角和阴影的效果
    * ListTile：用于显示行信息，具有如下属性：
      * title：为行指定title信息
      * subtilte：为行指定附加信息，如果isThreeLine为false，subtitle不能换行，如果为true，subtitle可以为两行
      * leading：为行定义icon
* ## 添加图片和资源
  * 在flutter程序中，通过pubspec.yaml中的flutter:assets：来app需要的资源
  * 资源变体：
    * 当一个资源在pubspec.yaml中被指定时，在该资源路径的同级子目录下，具有相同名称的资源也会被包含。
    ```yaml
    <!-- 
        例如，当.../background.png文件在yaml文件中被指定时，
        .../dark/background.png文件也会被包含的app中
     -->
     flutter:
      assets:
        - img/background.png
        - img/dark/background.png
    ```
  * 加载文本资源：
    * 可以通过rootBundle静态全局变量来加载资源
    * 在当前构建上下文中，通常推荐使用DefaultAssetBundle来获取文本资源，相比较于rootBundle，DefaultAssetBundle能够在运行时替换AssetBundle
    * 在上下文之外，可以使用rootBundle来导入文本资源
  * 加载图片资源：
    * 为了加载图片资源，可以通过AssetImage来加载
    * 使用依赖包中的图片，在通过AssetImage来获取图象时需要指定package包名
* ## Navigation（导航机制）
  * ### 从一个页面中传回数据
    * 可以通过Navigator.push方法来将新页面压入，该方法是异步方法，应该要通过await关键字等待返回结果。
    * Navigator.push方法接受如下参数
      * context（BuildContext）
      * MaterialPageRouter（builder:(context)=>return a widget here...)
    * 可以通过Navigator.pop方法回退界面并且返回结果数据
    * Navigator.pop方法接受如下参数
      * context
      * 返回的结果
  * ### 从一个页面向另一个页面中传参
    * 为了向另一个路由中进行传参，可以在MaterialPageRouter中为settings赋值RouteSettings，并且向arguments属性中加入参数
    * 在另一个页面中，可以通过Modal.of(context)方法来获取route对象
  * ### 在两个路由之间进行跳转
    * 在路由之间进行跳转可以分为如下部分
      * 创建两个路由
      * 使用Navigator.push来跳转到第二个路由
      * 使用Navigator.pop回退到第一个路由
  * ### 导航到命名路由中
    * 步骤：
      * 定义两个界面
      * 定义路由
      * 通过Navigator.pushNamed方法跳转到命名的路由
      * 通过Navigator.pop方法返回
    * 在创建命名路由时，需要在MaterialApp中指定initialRoute属性和routes属性
      * initialRoute属性指定开始时的路由路径
      * routes属性指定了所有可用的命名路由，以及为每个路由指定构建的Widget
    * 通过Navigator.pushNamed方法，可以跳转到指定的路由，而不用像push方法一样指定builder
  * ### 向命名路由传递参数
    * 可以通过pushNamed方法中的arguments参数向命名路由传递参数，传递的参数可以通过ModalRoute.of方法来获取，参数位于settings.arguments属性中
  * ### 为组件之间添加关联动画
    * 可以通过Hero组件为组件之间添加关联
* 状态管理
  * 如果一个widget中的状态不需要被其他widget所访问，那么可以在该widget内维护该状态
  * 如果一个widget中的状态要与其他widget共享，可以使用provider包来实现
    * 使用ChangeNotifierProvider，可以实现该节点下子节点之间的数据共享
    * 可以通过ChangeNotifier来维护数据
    * 使用Consumer来构建需要共享数据的节点