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
- ## Web表单
  - &lt;form&gt;标签
    - form标签一般会有两个属性，action和method
      - action：用来指定表单数据提交的目标url
      - method：用来指定提交数据用的方法，一般是get或post
    ```html
    <form action="/user/login" method="post">
      <input type="text" name="username" value=""
          placeholder="enter your username">
      <input type="password" name="password" value="">
      <input type="submit" value="Submit">
    </form>
    ```
  - label、input和textarea
    - &lt;label&gt;元素用来表示标签
    - &lt;input&gt;用来获取用户向表单输入的值
    - &lt;textarea&gt;用来获取用户输入的文本，可输入多行
    ```html
    <form action="/submit" method="post">
      <input type="text" name="useranme" value="" 
        placeholder="enter your password...">
      <input type="email" name="email" value=""
        placeholder="enter your email account...">
      <textarea name="msg" placeholder="enter your message..."
        cols="30" rows="20">
        enter your message...
      </textarea>
    </form>
    ```
  - 提交表单
      - 提交表单按钮可通过如下两种按钮实现
        - &lt;input type="submit"&gt;
        - &lt;button type="submit"&gt;
      - 上述两种方法都能提交表单，但是&lt;input&gt;元素中按钮内容只能通过向text属性中传入一个文本，但是&lt;button&gt;元素中可以嵌套更多的内容
      ```html
      <form action="/submit" method="post">
        <div>
          <label for="name_input">Name</label>
          <input id="name_input" type="text" name="name"
              placeholder="enter your name...">
          <label for="email_input">Email</label>
          <input id="email_input" type="email" name="email"
              placeholder="enter your email...">
        </div>
        <div>
          <input type="submit" value="Submit">
          <button type="Submit">
            <em>Submit</em>
          </button>
        </div>
      </form>
      ```
   - 表单结构
     - &lt;filedset&gt;和&lt;legend&gt;
       - &lt;filedset&gt;通常用于将表单中用于同意目的的一系列控件整合在一起
       - &lt;lengend&gt;通常被视为filedset中所有label的一部分
    ```html
    <form>
      <filedset>
        <legend>Pen Lip Bold<legned>
        <p>
          <input type="radio" id="ef" name="bold" value="ef">
          <label for="ef">ef</label>
        </p>
        <p>
          <input type="radio" id="f" name="bold" value="f">
          <label for="f">f</label>
        </p>
        <p>
          <input type="radio" id="m" name="bold" value="m">
          <label for="m">m</label>
        </p>
      </filedset>
    </form>
    ```
  - 表单元素
    - &lt;input&gt;
      - 该元素包含纯文本，不可以调整格式等
      - 该元素具有如下属性：
        - readonly：用户无法修改该input元素的内容，但是input内容会随着表单的提交被发送
        - disabled：用户同样无法修改input元素的内容，并且input元素的内容也无法随着表单的提交而提交
        - placeholder：占位符，用来描述该input元素中应该输入的信息
        - size：该input text的物理宽度
        - maxlength：用户可以输入到该input text中的字符数上限
      - input标签的种类
        - text：输入纯文本
        - password：password并不会对input中输入内容加以限制，但是其会隐藏用户输入的内容
        - hidden：type为hidden的元素，在表单中是不可见的，但是内容会在表单提交时一同被提交
        - Checkable组件
          - Checkable组件和其他组件不同，只有当该组件被选中时，才会在表单提交时被发送，如果该组件没有被选中，那么该组件的值并不会被发送，即使该组件的name属性并不为空，如果该组件被选中，而且没有value属性，那么value属性默认为"on"；其他组件只要存在name属性，都会被发送
          - checked属性：checked属性可以用来控制该Checkable组件是否默认被选中
          ```html
          <input type="checkbox" name="married" value="yes"
              checked id="cb_married">
          <label for="cb_married">married</label>
          ```
          - 类型：
            - checkbox
            - radio：一组按钮，具有相同的name属性，一组中只能选中一个按钮，被选中的name/value键值对会被发送给服务端
        - 按钮组件
          - &lt;input&gt;组件可以指定type为如下值之一：
            - submit：提交表单
            - reset：重置表单中所有组件值为其默认值
            - button：没有自动提交的效果，但是可以通过js来指定回调
          - 通过为&lt;button&gt;元素的type属性赋值也可以指定button类型为如上三个值之一。在没有为button的type属性指定值或指定无效值时，默认情况下type属性值为submit。
          - image组件：
            - 可以为type属性赋值为image，将会显示一个图像，点击图像可以执行提交表单的操作
            - 当点击image按钮执行提交操作时，点击按钮位置相对于按钮左上角的坐标会被添加到提交表单信息中，分别为${nameAttr.x}=xxx和${nameAttr.y}=yyy
            - 即使image组件没有name属性，其x和y坐标也会被提交，提交的name就为x和y
          ```html
          <input type="image" src="/imageSrc" alt="map"
            name="map" width="300" height="120">
          <!-- 
            点击按钮之后，点击位置相对图片左上角的相对坐标将会被提交，以如下形式：
              map.x=xxx&map.y=yyy
           -->
        - 文件传输组件
          - 可以将type属性指定为file，此时可通过file picker来选择文件上传
          - 相关属性：
            - accept：约束选择文件的种类，如image/*
            - multiple：如果想要选择多个文件，可以指定multiple属性（可以通过ctrl+left_click来完成多选）
          ```html
          <input type="file" name="file" accept="image/*"
              multiple>
          ```
      - 组件中的公共属性：
        - autofocus：自动获得焦点
          - 在一个document中，可设置表单中的一个组件自动获得焦点，如果一个document中有多个组件设置了autofocus，那么第一个设置autofocus的组件获得焦点
        ```html
        <input type="text" autofocus name="name" value="">
        ```
        - form:设置该组件关联的form表单
          - 组件可以被放置在表单外，表单外的组件和表单通过form属性想关联，form属性的值为form的id，当表单被提交时，表单外数据也被包含
        ```html
        <form id="form">
        </form>
        <input type="hidden" name="name" value="hidden_shiro" form="form">
        ```
  - html5新引入的input类型
    - email
      - 接受email格式的输入文本，在用户点击表单submit按钮时，会验证是否文本符合email格式，不符合会弹出警告
      - 可以通过指定multiple属性来允许接受多个email地址，多个email地址之间通过逗号分隔
    ```html
    <input type="email" name="email" value="" multiple>
    ```
    - search
      - 搜索框，用于提供特定样式的搜索框
    - tel
      - 接受电话号码，由于全世界不同地区有各种各样的电话号码形式，故而对输入文本的格式没有约束，输入文本中可以包含字母
    - url
      - 接受url，当输入文本缺乏协议名（如http、ftp）或者格式不正确时，浏览器会发出警告
    - number：
      - 接受一个浮点数格式的文本，可以设置最小值、最大值和文本
      - 在step属性没有指定时，step属性的默认值为1，因而该input只接受整数；要想让该input接受浮点数，可以设置step属性为any，此时可以接受任何浮点数
    ```html
    <input type="number" min="0" max="100" step="1"
      name="volume" value="1">
    ```
    - range
      - range可以提供一个滑动条来供用户选择数字
      ```html
      <input type="range" min="0" max="1" step="0.01">
      ``` 
    - 日期
      - datetime-local
        - 选择时间，无需指定时区信息，采用本地时区，包含年、月、日、时间
      ```html
      <input type="datetime-local" name="time">
      ```
      - month
        - 选择一个月份，包含年和月信息
      ```html
      <input type="month" name="month">
      ```
      - time
        - 选择一个时间
      ```html
      <input type="time" name="time">
      ```
      - week
        - 选择一个星期
      ```html
      <input type="week" name="week">
      ```
      - 上述属性还可以包含min，max，step等信息，在不包含时间的日期组件中，可以通过设置step属性来设置步长
    - color：
      - color控件提供了用来选取颜色的功能
      - 选取颜色的value值一直是hex形式，6位16进制数
      ```html
      <input type="color" name="color">
      ```
  - 其他表单控件类型
    - &lt;textarea&gt;
      - textarea标签可以用于输入多行文本，并且输入文本为纯文本的形式
      - textarea的主要属性：
        - cols：用于指定宽度，单位为字符平均宽度，默认为20
        - rows：用于指定高度，默认情况下为2
        - wrap：指定textarea的换行方式
          - soft：在渲染时，文本超过textarea的最大宽度时会换行，但是提交表单时并不会将换行符添加到内容中
          - hard：渲染时，文本超过textarea的最大宽度会黄行，并且提交表单时也会将换行符添加到内容中（在指定wrap为hard时必须显式指定cols的值）
          - off：不换行，而是为textarea提供水平滚动条
      - css resize属性：
        - 可以通过css中的resize属性来控制textarea的横向和纵向拉伸
        - resize属性可赋予的值：
          - both：横向纵向均可
          - horizontial：可横向拉伸
          - veritical：可纵向拉伸
          - none：不可以resize
    - 下拉框
      - 下拉框可以通过&lt;select&gt;标签来表示
      - option标签用来表示select标签的内容
        - option标签的selected属性用来设置默认选中的值
      ```html
      <select name="name">
        <option selected>Tifa</option>
        <option>Kazusa</option>
        <option>Alice</option>
      </select>
      ```
      - &lt;optgroup&gt;标签可以为option进行分组,optgroup标签的label属性可以为同一option组的标记设置描述
      - 若option标签具有value属性，那么在表单被提交时，键值对中的value是value属性的值，若option不具有value属性，option标签内包含的内容则会随着表单提交而发送
      - select标签支持multiple属性，可以选中多个option
      - select标签支持size属性，可以设置下拉框同时可看见多少个option
      ```html
      <select name="name">
        <optgroup label="male">
          <option value="h">Haruki</option>
          <option selected value="t">Takumi</option>
        </optgroup>
        <optgroup label="female">
          <option value="k">Kazusa</option>
          <option value="a">Asuna</option>
        </optgroup>
      </select>
      ```
    - 带自动补全的控件
      - &lt;datalist&gt;标签可以为input元素提供自动补全的数据，当用户在input中输入数据时，可以在datalist中匹配数据并且给用户以补全提示
      ```html
      <input type="select" list="list_srh" name="search">
      <datalist id="list_srh">
        <option>Touma Kazusa</option>
        <option>Kitahara Haruki</option>
        <option>Rikako Natsumi</option>
        <option>Ogiso Setsuna</option>
      </datalist>
      ```
      - 如果为type=“range”的input标签指定datalist，那么datalist对应的值上都会有一个小刻度
    - 进度条
      - 进度条可以通过&lt;progress&gt;标签来展示
      ```html
      <progress max="100" value="60"></progress>
      ```
    - &lt;meter&gt;
      - meter元素长常用来表示磁盘的占用量等，并且用green、yellow、red等颜色来表明当前状态
      - meter元素的主要属性
        - min：最小值
        - max：最大值
        - low
        - high
        - optimum：最佳值
      - optimum值位置：
        - 位于min和low之间时，(low,hight)为avg，[hight,max]为wrost
        - 位于low和high之间时，[min,low]和[high,max]都为avg
        - 位于high和max之间时，[min,low]为wrost，(low,high)为avg
      - 颜色：
        - preferred为绿色
        - wrost为红色
        - avg为黄色
      ```html
      <meter min="0" max="256" low="100" high="200" optimum="50"
        value="255">255GB/256GB</meter>
      ```
  - 表单的伪类
    - :required和:optional
      - 关于&lt;input&gt;、&lt;select&gt;、&lt;textarea&gt;等标签的required属性：
        - 一旦上述标签指定了required属性，那么在提交表单时，上述标签必须已经手动填写了相关信息或是通过value睡醒那个有默认值，不能为空
        - 可以通过:optional和:required伪类来为不同的input组件采用不同样式
        - checkbox，如果该checkbox未被选中则表单提交时不会被添加到数据中，若checkbox被指定为required，那么在提交表单时该checkbox必须被勾选
      ```html
      <style rel="stylesheet">
        input:required {
          background-color:red;
        }

        input:required {
          background-color:gray;
        }
      </style> 
      <input type="text" required name="text_required">
      <input type="text" name="text_unrequired">
      ```
    - :invalid和:valid
      - :invalid和:valid判断通常如下：
        - 如果input元素没有约束，那么都是valid的
        - 如果input元素标记为required，但是值为空，那么为invalid
        - 如果input元素type为email或者number但是不满足条件，那么也为invalid
    - :in-range和:out-of-range
      - 对于input类型为数字的输入框，如果输入位于min和max属性之外，那么该input元素会匹配:out-of-range伪类，否则会匹配:in-range伪类
      - 匹配:in-range的元素同样会匹配:valid，匹配:out-of-range的伪类同样会匹配:invalid，但是:in-range/:out-of-range指定的范围比:valid/:invalid更精确，可以用来指定更为复杂的样式
    - :enabled和:disabled
      - :enabled和:disabled判断如下
      - 可以通过disabled属性来指定组件是否为enabled或者disabled
    - :read-only和:read-write
      - 类似与:enabled和:disabled,:read-only和:read-write通常可以用来选择那些是否可以编辑的组件，但是readonly标记的组件内容会随着表单被提交，而disabled组件标记的内容则不会随着表单提交
      - 可以通过readonly属性来标记input是否可以被修改
    - 针对radio和checkbox组件的伪类
      - :checked、:default、:indeterminate
      - :checked
        - :checked伪类用来选择该input是否被选中
      - ：default
        - ：default伪类用来选中那些在导入页面时默认被选中的input组件
        - 即使后来该组件被取消选中状态，:default依然会选中该组件
      - ：indeterminate
        - ：indeterminate用来选中那些没有确定状态的组件，既不是选中状态，也不是非选中状态
        - :indeterminate状态如下：
          - 多个具有相同name属性的radio，但是没有选中任何一个
          - 通过js将checkbox的indeterminate属性设置为true
          - progress元素没有value属性
  - 浏览器端的表单数据验证
    - 对于表单控件，有如下两种方式进行数据验证
      - html5组件内置的验证方式
      - 通过js来进行自定义的验证
    - 内置验证方式
      - 内置验证方式通常通过html5表单控件的属性来完成对输入数据的约束，该方法验证客户端数据并不需要通过js语言即可完成
      - 内置属性如下：
        - required：设置该空间内容是否需要填充且不为空
        - maxlength/minlength：指定输入字符串数据的最长长度和最短长度约束
        - min/max：对于数值类型，指定输入值的最大/最小值
        - type：指定输入值是否为email类型、number类型或是其他类型
        - pattern：通过正则表达式来指定输入数据的类型
      ```html
      <!-- 通过正则表达式验证input -->
      <style>
        input:valid {
          border: 1px solid black;
        }
        input:invalid {
          border: 2px solid red;
        }
      </style>

      <input type="text" required 
          pattern="^[Tt]ouma [Kk]azusa$">
      ```

      



