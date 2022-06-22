# dom
- ## js中dom模型的简介
  - js的dom中常用对象：
    - window：浏览器用来载入页面的tab，通过js中的window对象，用户可以通过调用window对象中的方法完成如下操作：
      - 返回窗口大小
      - 操作导入window中的document
      - 存储特定页面的数据
      - 在当前document中添加handler
    - navigator：可以通过该对象来检索浏览器的状态和身份信息
    - document：代表载入window的html文档
  - dom树形结构：
    - root node：在dom树结构中，根节点总是html节点
    - 子节点：直接位于另一个节点内的节点
    - 后代节点：如果一个节点位于另一个节点内，不管是直接位于还是间接被包含
    - 父节点：如果一个节点直接包含另一个节点
    - 兄弟节点：如果两个节点位于dom树中的同一层，并且具有相同的父节点，那么他们就是兄弟节点
- ## document api查找节点元素
  - querySelector：
    - document.querySelector()会返回第一个匹配的元素
        ```javascript
        document.querySelector("p")
        ```
  - querySelector:
    - 返回所有满足条件的元素，按照NodeList的形式返回
        ```javascript
        document.querySelectorAll("p")
        ```
- ## document api创建新元素
  - document.createElement:
    ```javascript
    let node=document.createElement('p')
    let p_node=document.querySelector('p')
    p_node.apendChild(node)
    ```
  - node.appendChild:通过该方法可以为节点插入新的子元素
  - document.createTextNode：创建新的文本节点（类似与#text的子节点）
- ## document api 移除元素
  - parentNode.removeChild：移除子元素
    ```javascript
    let par_node=document.querySelector("div")
    let child_node=document.querySelector("div > p")
    par_node.removeChild(child_node)
    ```
  - childNode.remove:对子元素可以直接调用remove
    ```javascript
    let child_node=document.querySelector("div > p")
    child_node 
    ```
- ## js修改css样式
  - 可以通过修改HTMLElement.style中的属性来通过js修改html页面中元素的样式，其会通过inline style（html元素的style属性）来修改页面样式
    ```javascript
    let node=document.querySelector("p")
    node.style.background="grey"
    ```
  - 除了可以通过修改inline style来动态修改元素属性外，还可以预先在css中为各个class指定样式，然后在运行时动态的通过js来修改元素的class属性，此时会为元素应用不同的样式
    ```javascript
    let node=document.querySelector("p")
    node.setAttribute('class','white-style')
    ```
  

