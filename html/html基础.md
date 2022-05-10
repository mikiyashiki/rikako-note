# html基础
- ## html中知识要点
  - html中空格
    - 不管在html元素中添加了多少空格或者换行，其连续的多个空格都会被替换为一个空格，块元素中的换行符会被取消，除非显式指定<br>
  - html中特殊符号：
    - 在html中，< > ' " 都是特殊的符号，其都是html语法的一部分，故而在文本中如果存在这些特殊字符，可以通过特殊的引用来表示该特殊字符
    - 特殊的用来表示该类字符的表达式以&开头，以；结尾，具体如下
    ```shell
    <   可以用  &lt; (less than)
    >   可以用  &gt; (greater than)
    "   可以用  &quot; 
    '   可以用 &apos;
    &   可以用 &amp;

    // 例如，可以在<p>中包含如下内容
    <p>This is content of element &lt;p&gt;!</p>
    //  其会显示如下内容
    This is content of element <p>!
    ```
  - html中评论：
    ```html
    <!-- comment content -->
    ```
  - html head:
    - head标签中的信息并不显示在html页面中，html中包含的是html页面的元信息
    - head中可以包含如下信息：
      - title：该页面显示在title标签栏中的标题内容
      - meta：meta标签用来定义元信息
        - 通过meta元信息指定文档使用的字符集：
        ```html
        <meta charset="utf-8">
        ```
        - 通过meta元信息为页面指定作者和页面描述信息：
        ```html
        <!-- 
          name: 用来指定元信息元素的类型
          content：用来指定元信息元素的内容
         -->
        <meta name="author" content="Rikako Wu">
        <meta name="description" content="information description">
        ```
      - 为页面制定favicon图标：
        - 在index.html路径下防治ico图标
        - 在head元素中添加如下标签
        ```html
        <link rel="icon" href="favicon.ico" type="image/x-icon">
      - 向页面中添加css样式
        - 用rel指定该link为stylesheet
        - 用href来指定css样式表的路径
        ```html
        <link rel="stylesheet" href="./main.css">
        ```
      - 向页面中添加script脚本
        - 指定defer，可以在页面完成解析后再执行引入的js脚本
        - 用src属性指定js脚本路径
        ```html
        // 除了可以引入外部脚本外，还可以在script标签中加入
        //    js代码
        <script src="./main.js" defer></script>
        ```
      - 为文档指定语言信息
        - 可以在html标签中制定lang属性来为文档指定使用的语言
        ```html
        <html lang="en-US">
        ```
  - html body中的重要元素
    - 强调某些单词
      - em:通过em标签，通常会通过斜体的效果来加强某些单词
      - strong：通过strong标签，通常会通过加粗的方式来强调某些单词

