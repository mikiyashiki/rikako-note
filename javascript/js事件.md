# js事件
- ## js event
  - 对于可以触发事件的对象，其会有一个addEventListener方法，通过该方法，可以为对象的特定事件注册handler。addEvnetListener接受两个参数：
    - event name
    - event handler
  - 对于js语言，在不同的环境下，event model可能会有所不同，例如在web浏览器环境下和nodejs环境下，event model就不完全相同
  - 可以通过removeEventHandler来移除handler，该方法同样接收两个参数，event name和handler
- ## 在js中为事件添加复数个handler
  - 通过多次调用addEventHandler，可以为同一个事件添加多个handler，在事件发生后，多个handler都会被调用
- ## Event Handler Properties
  - 在js中，如果一个对象能够触发某个事件，通常该对象会有一个onxxx属性，例如onclick、onmouseover等。可以通过为该属性赋值一个函数来为该事件添加handler
  ```javascript
  let p=document.querySelector("#bt")
  p.onclick=()=>{
      alert("button is clicked")
  }
  ```
  - 但是，通过为对象的onclick属性赋值，无法为该对象的同一个事件添加多个handler，对onclick属性的多次赋值，之后的赋值操作会覆盖前面的赋值操作
- ## inline event handler（不推荐）
  - 可以在html文件中为标签的onxxx属性指定调用的js方法来为事件添加handler，但是这种方法永远不应该被使用
  - 使用inline event handler时，如果多个元素都为相同事件添加相同的handler，那么相同的函数调用代码将会在html文件中重复多次，修改会花费大量时间
  - 并且，使用inline event handler会令js代码和html代码糅合在一起，最好将js代码和html代码分开
- ## event对象
  - handler的参数中可以含有一个event对象，用来记录触发事件的信息，例如event.target记录了指向触发事件的对象的引用
  - 可以通过event.preventDefault()方法来取消浏览器为该事件定义的默认处理。
  - 可以通过event.stopProgation()方法阻止该事件的传播
  - 通过return false，默认会同时调用event.preventDefault和event.stopProgation
- ## 事件传播的三个阶段
  - capturing阶段：
    - 捕获阶段首先会检查触发事件元素的最外层元素（html），检查其是否注册了capture阶段的handler，如果注册了就运行handler
    - 然后其会移动到html内部元素并做相同的事情，如果元素有注册capture阶段handler就执行，并依次向内移动，直到遇到触发事件元素的直接父元素
  - target阶段：
    - 首先，target阶段会检查EventTarget是否有为event注册的handler，如果有就运行它
    - 如果，event的bubbles为true，其会将event事件传播给EventTarget元素的父元素，并以此向上传播，直到到达html元素；如果bubbles为false，则event不会被向上传播给EventTarget的任何父元素
  - bubbling阶段：
    - 检查是否EventTarget的直接父元素为bubbling阶段注册了handler，如果有就运行
    - 对EventTarget父元素的父元素做同样的事，检查是否为bubbling阶段注册有handler，如果有就执行，依次向上传播，直到到达html元素
  - >对于所有的js事件，都会经历capturing阶段和target阶段，但是只有bubbles属性为true的事件才会经历bubbling阶段
  - > 对于target阶段，对于EventTarget，无论是为capturing阶段注册的handler还是为bubbling阶段注册的handler都会被执行
  - > addEventListener默认情况下注册的阶段是bubbling，可以为其制定useCapture为true(默认为false)来指定注册的listener为capturing阶段
  - > 可以在event handler中调用event.stopPropagation来阻止事件在bubbling过程中向上传播
- ## event.target和event.currentTarget
  - event.target：触发事件的元素
  - event.currentTarget：当前handler所附加到的元素（在bubbling阶段时间传播到的上层祖先节点）