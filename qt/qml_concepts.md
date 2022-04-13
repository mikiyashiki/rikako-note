  # qml基本概念
  * ## 坐标系概念
    * qml组件的坐标系为迪卡尔坐标系，以屏幕水平方向为x轴，垂直方向为y轴，分为以下两种： 
      * scene坐标系：坐标系通常与根组件的组件坐标系相同，（0,0）位置与想要渲染的画布的左上角相对应
      * item坐标系：通常，若当前组件不是根组件，则当前组件的坐标x，y属性是相对与父组件的组件坐标系的    
  * ## visual parent（可视化父组件）
    * qml对象的visual parent和qobject parent是两个不同但是相关连的概念。    
      * object用于管理qml对象的生命周期和内存管理。
      * 在使用qtquick模块时，该模块中所有类型都包含了一个visual parent的概念，用来决定该组建在场景渲染时的父item坐标系。每个item都有一个parent属性，该属性对应的值就是该item的visual parent。如果某item的parent属性为null，则该item为渲染该场景的根item。
    * 如果将一个对象赋值给一个item的data属性，那么该对象在qojbect层次结构中会变成item对象的子对象;如果该对象还是item对象，那么该对象还会在visual parent层次结构中变为item对象的子对象
      * 方便起见，如果一个item中声明另一个item，并且没有将该item赋值给外层item的仍然属性，那么将会默认将内层item添加到外层item的data属性，内层item变为外层item的qobject子对象和visual层次结构的子对象
      * visual parent可以在运行时通过修改parent属性改变，故而visual parent和qobject层次的父对象并不一定相同
* ## scene中的item的渲染顺序
  * 默认情况下，归属于统一父item的兄弟子item之间，后声明的item渲染在先声明的item之上。若item a渲染在item b之上，那么item a子树中所有子item都渲染在以item a为根的子树之上，即使item a中某个子item的z属性大于item a子树中某个item的z属性值。若想将item a子树中的item渲染到item b子树之上，只能够改变item a的z属性值令其渲染在item b之上。