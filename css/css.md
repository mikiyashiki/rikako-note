# CSS
- ## css选择器
  - 根据标签名选择
    ```css
    <!-- 
        多个标签类型之间可以用逗号隔开
     -->
    h1,p,li {
        color:red;
    }  
    ```
  - 根据class进行选择
    - 通过class属性来进行选择
    ```css
    .disk-block {
        border:1px black dashed;
    }   
    ```
    - 通过标签名和class同时来进行选择
    ```css
    li.pink-css,div.pink-css {
        color:pink;
    }
    ```
  - 后代选择器
    - 后代选择其会根据标签的位置来进行选择,多个标签之间通过空格隔开
    ```css
    li em {
        font-style: italic;
    }
    li span.pink-style {
        color:pink;
    }
    ```
  - 相邻兄弟选择器
    - 相邻兄弟选择器会选择其相邻的下一个兄弟节点
    ```css
    li + li {
        color:red;
    }
    ```
  - 子选择器
    - 相比与后代选择器，子选择器只会选择其节点的直接后代，间接的后代不会被选中
    ```css
    li > em {
        color:pink;
    }
    ```
- ## css使用方法
  - 引用外部css文件
  ```html
  <head>
    <link rel="stylesheet" href="style.css">
  </head>
  ```
  - 内部样式
  ```html
  <head>
    <style>
        p {
            color: red;
        }
    </style>
  </head>
  ```
  - 内联样式
    ```html
    <p style="font-size:1.5em;color:red;">Hello</p>
    ```
- ## 多条样式规则应用于同一个元素
  - 多个样式规则的选择器范围相同
    - 在选择其范围相同的情况下，后出现的选择器样式会覆盖先前出现的选择器样式
    ```css
    p {
        color:red;
    }
    p {
        color:blue;
    }
    /* 后出现的p样式会覆盖之前出现的p样式，最终颜色为blue */
    ```
  - 多个样式规则的选择器范围不同
    - 如果多个样式的选择器范围不同，那么范围更小更特殊的选择器样式胜出
    ```css
    .red-p {
        color:red;
    }
    p {
        color:blue
    }
    /* 此时类选择器比元素选择器更特殊，故而最终颜色为red */
    ```
  - 在同一个选择器中重复指定样式
    - 在同一选择器中重复指定样式，位于后面的样式会覆盖位于前面的样式
    ```css
    p {
        color:blue;
        color:red;
    }
    ```


