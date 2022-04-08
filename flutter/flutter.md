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