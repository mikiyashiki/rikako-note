# js中的import和export
* ## export
  * js中的导出方式可以分为两类
    * named export： 同一个文件中可以导出多个named export
    ```javascript
    // 导出预先定义的变量，按列表的形式
    export {myFunction,myVariable}

    // 导出重命名的变量，以列表形式
    export {var1 as name1,var2 as name2...}
    ```
    * default export：每一个module只允许有一个default export
    ```javascript
    export {var as default}
    export default function () {}
    export default class
    ```
  * export from 语法：
  ```javascript
  export {default as name1,name2} from './other_modules.js'
  // 等效于
  import {default as name1,name2} from './other_modules.js'
  export {name1,name2}
  ```
* ## import
  * import特性：
    * import用来导入被其他module导出的read-only绑定值
    * import无法在嵌入的脚本（script标签中）使用，除非其script标签的type属性为module
  * import语法
  ```javascript
  // 导入default export
  import defaultExport from './other_module.js'

  // 导入module中所有输出到命名空间
  import * as other_module from './other_module.js'
  // 引用时可以将other_module视为引用导入module中导出元素的命名空间
  other_module.exportFunction()

  // 导入单个元素
  import {myExport} from './other_module.js'

  // 导入时进行重命名
  import {myLongVariableName as shortName} from './other_module.js'

  // 导入default export
  import defaultExport from './other_module.js'
  ```
- ## dynamic import
  - 动态加载：
    - 静态加载会在启动时加载大量不会马上被用到的module，这回花费大量的时间
    - 有些module在启动时可能并不存在
    - 动态加载相比与静态加载，其可以通过import()函数在需要的时候才对module进行加载
  - import函数详情
    - import函数是一个异步函数，其接收一个module路径字符串并且返回一个promise，并且，该方法支持await关键字

