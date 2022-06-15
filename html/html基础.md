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
    - 超链接(hyperlink)
      - 超链接语法如下，通常用于在不同的document之间进行跳转
      - 可以通过title属性来为超链接添加一些信息，当鼠标hover时，添加信息将会以tooltip的形式显示
      ```html
      <a href="https://www.baidu.com"
          title="homepage for baidu">link to baidu homepage!</a>
      ```
      - 通过hyperlink链接到文档的特定部分，而非是跳转到文档顶部
      ```html
      <!-- 
        通过在URL之后添加#{元素id}，可以跳转到目标页面的id对应元素
        # 如果想要在当前页面内完成跳转，可以省略URL
       -->
      <a href="https://www.baidu.com/#ele-id"
          title="link to baidu ele-id">link to baidu ele-id!</a>
      ```
      - 当想要下载链接到的资源而不是在浏览器中打开时，可以在hyperlink中制定download属性，并且为其赋值一个字符串用来表示文件名
      ```html
      <!-- 
        当通过download属性指定想要下载的文件时，需要满足同源策略，否则download属性失效
       -->
      <a href="URL-TO-RESOURCE" 
          download="file-name">download file</a>
      ```
    - 描述性列表（description list）
      - dl可以为每一个列表项添加dt（description term）和dd（description definition）
      - 可以为同一个dt添加多个dd
      ```html
      <dl>fruits
        <dt>apple</dt>
        <dd>
          fruit with red color!
        </dd>
        <dt>banana</dt>
        <dd>
          fruit with yellow color!
        </dd>
        <dd>
          fruit with bar shape!
        </dd>
      </dl>
      ```
    - 引用（quote）
      - 引用可以分为块级引用和内联引用
      - blockquote
      ```html
      <blockquote cite="resouce path">
        quote content...
      </blockquote>
      ```
      - q
      ```html
      <p>This is an inline quote: <q cite="resource path">
        quote content...</q></p>
      ```
    - 缩写（abbr）
      - 缩写用来标注缩写，并且可以在title属性中指定全称，全称在hover时以tooltip的形式显示
      ```html
      <p><abbr title="hypertext markup language">HTML</abbr> 
      is used commonly in web area!</p>
      ```
    - 上标或下标（sub和sup）
      - sup标记用来处理上标，sub标记用来处理下标
      ```html
      <p>water is H<sub>2O</p>
      ```
    - 标记代码
      - &lt;code&gt;用来表示通用的代码部分
      - &lt;pre&gt;用来保持编辑器中的格式
      - &lt;kbd&gt;用来表示命令行输入
      - &lt;samp&gt;用来表示程序输出
      - &lt;var&gt;用来代表变量
  - html结构
    - header
      - &lt;header&gt;
    - navigation bar
      - &lt;nav&gt;
    - main content
      - &lt;main&gt;
        - &lt;main&gt;标签中通常含有&lt;section&gt;,&lt;div&gt;,&lt;article&gt;等元素
    - side bar
      - &lt;aside&gt;通常被放置在&lt;main&gt;中
    - footer
      - &lt;footer&gt;
  - &lt;hr&gt;
    - &lt;hr&gt;会在页面中间创建一条横线，用来分割之前和之后的内容
    ```html
    <div>
      Section A
    </div>
    <hr/>
    <div>
      Section B
    </div>
    ```
- ## html中添加多媒体
  - html中添加图片
    - 在html页面中添加图片可以通过&lt;img&gt;元素来实现
    - 可以为图片指定title属性，当鼠标hover时会通过tooltip显示信息
    ```html
    <!-- 
      可以为img指定一个描述性的文本，当img无法被加载（网络问题、资源不再存在）时，可以显示alt指定的文本
     -->
    <img src="./img/waifu.png"
        alt="Picture of Waifu"
        width="300"
        height="200">
    ```
    - &lt;figure&gt;和&lt;figcaption&gt;
      - &lt;figure&gt;可以为内容添加说明性文字
      ```html
      <figure>
        <img src="./img/waifu.jpg"
            alt="waifu">
        <figcaption>
          Touma Kazusa in WA2
        </figcaption>
      </figure>
      ```
  - html中添加video
    - 可以通过&lt;video&gt;来向html中嵌入video
    - 在video的tag中可以嵌套内容，当当前的浏览器不支持video tag时，会显示嵌套的内容
    ```html
    <video src="waifu.mp4" controls>
      <p>Sorry, your broswer doesn't support &lt;video&gt; tag</video></p>
    </video>
    ```
    - 为&lt;video&gt;指定多个不同格式的视频源，让视频能够在不同浏览器平台都得到播放
    ```html
    <video controls>
      <!-- 
        type属性通常用来指定视频文件的mime类型，浏览器可以通过type属性快速跳过不支持的视频类型
        -->
      <source src="waifu.mp4" type="video/mp4">
      <source src="waifu.webm" type="video/webm">
    </video>
    ```
    - video的其他属性：
      - autoplay：页面加载时直接开始播放视频
      - width/height：设置宽度和高度
      - loop：视频播放完后循环播放
      - muted：视频以静音的形式播放
      - poster：在视频播放前，poster指定url的图片将会被显示
      - preload：用来缓存文件
        - “none”：不缓存文件
        - “auto”：缓存文件
        - “metadata”：只缓存元信息
  - html中添加audio
    - 可以通过&lt;audio&gt;来添加音频数据
    ```html
    <audio controls>
      <source src="music.mp3" type="audio/mp3">
      <source src="music.ogg" type="audio/ogg">
    </audio>
    ```
  - html中添加字幕
  ```html
  <video controls>
    <source src="waifu.mp4" type="video/mp4">
    <source src="waifu.webm" type="video/webm">

    <!-- 
      kind: 指定是subtitles, captions, descriptions
      src： 字幕文件路径
      srclang：指定字幕的语言种类
      label：指定标签，此处告诉用户是西班牙语字幕
     -->
    <track title="subtitles" src="subtitles_es.vtt"
      srclang="es" label="Spanish">
  </video>
- ## html中嵌入其他元素
  - &lt;iframe&gt;元素的使用
    - &lt;iframe&gt;通常用于向文本中嵌入其他文档或来源于其他网站的视频信息，其有如下属性
      - allowfullscreen：如果存在该属性，iframe元素可以被全屏化
      - src：用于指向想要嵌入元素的url
      - width/height：用于指定iframe元素的宽度和高度
      - fallbackContent：如果当前浏览器不支持iframe，会显示fallbackContent中的内容
      - sandbox：沙箱，安全性更加高
      ```html
      <iframe src="https://bilibili/video/path" allowfullscreen
          width="600" height="400" sandbox>
          <!-- 
            fallback content
           -->
          <p>
            Your broswer doesn't support &lt;iframe&gt;!
          </p>
      </iframe>
      ```
      - iframe安全
        - sandbox是一个容器，在容器中的代码可以被正确的使用和测试，但是无法对容器外的其他部分造成任何伤害。
        - 没有在sandbox中运行的内容可以运行js代码、提交表单、弹出窗口，会造成安全问题。故而，在使用sandbox时，应该使用没有参数的sandox属性，应用所有的安全约束
        - 不要同时给予sandbox以allow-script和allow-same-origin权限，否则可以通过allow-same-origin绕过同源策略，并且通过allow-script运行js脚本关闭沙箱
        - 通过X-Frame-Options的HTTP响应头字段，可以设置SAMEORIGIN或者DENY，当设置DENY时，当前页面无法被嵌入到其他页面的iframe中，如果设置为SAMEORIGIN，当前页面可以被嵌入到来自同一origin的其他页面中
- ## 响应式图像
  - 为不同的屏幕宽度选用不同的分辨率图片
  ```html
  <img srcset="img-480w.png 480w,
                  img-600w.png 600w"
        sizes="(max-width:500px) 480px,
                  600px"
        src="img-480w.png"
        alt="img...">
  ```
  - 为不同物理像素/逻辑像素值的设备来选用不同分辨率图片
  ```html
  <img srcset="img-480w.png,
              img-720w.png 1.5x,
              img-960w.png 2x"
        src="img-480w.png"
        alt="img...">
  ```
  - 为不同屏幕宽度的设备选用不同的图片
  ```html
  <picture>
    <srouce media="(max-width:599px)" srcset="img-1.png">
    <source media="(min-width:600px)" srcset="img-2.png">
    <img src="img-1.png" alt="img...">
  </picture>
  ```
- ## html表格
  - &lt;table&gt;：表示table标签
  - &lt;th&gt;：表示表格的行、列标题
  - &lt;tr&gt;：表示表格的行
  - &lt;td&gt;：表示表格的单元行
  ```html
  <table>
    <tr>
      <th>Name</th>
      <th>Age</th>
    </tr>
    <tr>
      <td>Touma</td>
      <td>17</td>
    </tr>
    <tr>
      <td>Ogiso</td>
      <td>17</td>
    </tr>
  </table>
  ```
  - 可以通过rowspan和colspan属性来控制单元格跨行和列的数目
  ```html
  <table>
    <colgroup>
      <col style="background-color:pink">
    </colgroup>
    <tr>
      <th colspan="2">
        Waifu
      </th>
    </tr>
    <tr>
      <th>Japan</th>
      <th>France</th>
    </tr>
    <tr>
      <td>Touma</td>
      <td>Charles</td>
    </tr>
    <tr>
      <th colspan="2">waifu</th>
    </tr>
    <tr>
      <td rowspan="2">Kazusa</td>
      <td>Japan</td>
    </tr>
    <tr>
      <td>Austria</td>
    </tr>
  </table>
  ```
  - colgroup和col
    - &lt;colgroup&gt;：col的容器
    - &lt;col&gt;：col通常用来指定表格中列的样式
    ```html
    <table>
      <colgroup>
        <col>
        <col style="background-color:pink">
      </colgroup>
      <tr>
        <th>Name</th>
        <th>Age</th>
      </tr>
      <tr>
        <td>Touma</td>
        <td>17</td>
      </tr>
    </table>
    ```
  - caption：添加描述性文本，描述性文本位于表格的正上方
  ```html
  <table>
    <caption>Waifu Table</caption>
    <tr>
      <th>Name</th>
      <th>Age</th>
    </tr>
    <tr>
      <td>Touma</td>
      <td>17</td>
    </tr>
  </table>
  ```
  - thead,tbody,tfoot
    - thead通常用来标注表头的行，即&lt;th&gt;标签所在的行
    - tfoot通常用来标注表尾信息，比如对上述内容之和的SUM统计
    - tbody通常用来标注表格除thead和tfoot之外的其他信息
    ```html
    <table>
      <colgroup>
        <col>
        <col style="background-color:gray">
      </colgroup>
      <caption>Cost Summary</caption>
      <thead>
        <tr>
          <th>Term 1</th>
          <th>Term 2</th>
        </tr>
      </thead>
      <tfoot style="font-weight:bold;font-size:1.2em;color:cyan">
        <td>3</td>
        <td>5</td>
      </tfoot>
      <tbody>
        <tr>
          <td>1</td>
          <td>2</td>
        </tr>
        <tr>
          <td>2</td>
          <td>3</td>
        </tr>
      </tbody>
    </table>
    ```
  - 可以在td中嵌套table表格
    ```html
    <table>
      <thead>
        <tr>
          <th colspan="2" style="text-align:center">Waifu</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>
            <table>
              <tr>
                <th>Name</th>
                <th>Age</th>
              </tr>
              <tr>
                <td>Touma</td>
                <td>17</td>
              </tr>
            </table>
          </td>
          <td>
            <table>
              <tr>
                <th>City</th>
                <th>Job</th>
              </tr>
              <tr>
                <td>Tokyo</td>
                <td>Pianoist</td>
              </tr>
            </table>
          </td>
        </tr>
      </tbody>
    </table>
    ```
  - scope属性：在th标签中，具有scope属性，可以指明该th对应的是行标题、列标题、行组标题、列组标题，scope标签对应如下值：
    - row
    - col
    - rowgroup
    - colgroup
- Web表单
  - 
      

      



