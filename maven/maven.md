# maven
* ## maven的pom文件结构
  * project：project元素是pom文件的顶层元素
  * modelVersion：pom文件使用的model版本
  * groupId
  * artifactId
  * version
  * name：maven项目的名称
  * url：项目的url资源路径
  * propertities：包含可以在pom文件中任何位置访问的变量
  * dependencies：定义该项目使用的依赖项
  * build：定义项目的目录结构和插件的管理
  * packaging：指明项目打jar包还是war包
* ## maven命令
  * mvn compile：会对项目进行编译，并且将编译后的字节码放置到basedir/target/classes目录下
  * mvn test：对项目的main源码进行编译，并且再对test源码进行编译，并执行单元测试
  * mvn package：编译项目并且创建jar包，创建的jar包将会被放置到basedir/target目录下
  * mvn install：生成项目对应的jar包并且将生成的jar包安装到本地仓库中（homedir/.m2/repository）
  * mvn clean：再开始前移除target目录中所有的文件
* ## SNAPSHOT
  * < verssion >标签值中含有snapshot代表该artifact版本并不是稳定或者不变的，而是一个快照版本，是可能被修改的
* ## plugin使用
  * 当想对项目的构建过程进行自定义时，需要再pom文件的build标签中添加plugin：
 ```pom
 <!-- 
        将项目的source和target设置为1.8，
            source指定了编译的源码版本
            target指定了产生字节码文件兼容的jre平台版本
            source和target可以不同
  -->
 <build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-compiler-plugin</artifact>
            <version>3.3</version>
            <configuration>
                <source>1.8</source>
                <target>1.8</target>
            </configuration>
        </plugin>
    </plugins>
 </build>
 ```
* ## 向maven项目中添加资源
  *  如果想要向maven项目中添加资源，那么将资源复制到basedir/src/main/resources或者basedir/test/main/resources目录下，在项目生成jar包时，会将资源复制到jar包中的classpath目录下
*  ## maven项目添加依赖
   *  通过向pom文件中添加dependency可以为项目添加依赖项
   *  依赖至少需要有如下元素：
      *  groupId
      *  artifactId
      *  version
      *  scope可选：指定依赖的作用范围
         *  compile
         *  test
         *  runtime
* ## maven项目中parent
  * 可以为父maven项目指定modules来指定子项目
  * 可以为子项目指定parent元素来指定父项目
  * 可以在父项目的路径下调用mvn package同时打包多个项目
* ## maven filtering
  * 通过filter，可以在资源文件中使用${}来引用环境变量、定义在pom文件中的变量、定义在外部文件中的变量（需要在filters中指定）、在命令行中通过-D指定的变量
  * 在对资源文件实施filering时，需要指定资源文件路径，并且在resource标签中将filtering属性指定为true
* ## maven生命周期
  * maven项目的生命周期分为如下三种
    * default lifecycle
    * clean lifecycle
    * site lifecycle：负责web项目的建立和发布
  * maven项目的default生命周期：
    * validate：验证项目是否正确
    * compile：编译源代码
    * test：通过单元测试来对编译好的源代码进行测试
    * package：将编译好的源码进行打包，打包成jar包或者是war包
    * verify：对集成测试的结果进行检查
    * install：将打包后的结构安装到本地仓库中
    * deploy：在构建环境中完成，将软件包部署到远程仓库中
  * 当调用上述生命周期的命令时，只需要调用最后一个阶段名称，之前的阶段会被默认执行（eg：如果调用mvn package，那么之前的阶段例如validate、compile、test、package也会被默认执行）
  * 如果在调用生命周期命令之前想要清理先前的残留，可以在生命周期之前加入clean命令清理先前留下的target目录，eg：mvn clean package
  * maven的build阶段由plugin goals构成
    * 每个plugin goal都会绑定到0或者多个build phase
    * 每个build phase都绑定了0或者多个plugin goals，如果一个build phase没有绑定plugin goals，那么它将不会被执行，如果绑定了多个plugin goals，多个plugin goals都会被执行
* ## maven的打包方式
  * 已知build phase由多个plugin goal组成，那么，在构建项目时，为了将plugin goal和build phase相绑定，可以通过packaging标签指定打包的方式（如jar，war，pom，ear（除了包含war、jar包外，还包含EJB组件）
  * 如果指定packaging标签的打包方式为jar，那么maven项目将会为每个build phase都指定一个plugin goals列表；而对于打包成pom的项目，只会为install和deploy的阶段绑定plugin goals
* ## maven插件
  * maven通过plugin来提供plugin goals，plugin类似于dependency，也具有groupId，artifactId，version。plugin可包含多个plugin goals，并且可以为插件指定executions来指定执行的goal，并且可以指定phase将goal与build phase相关联。
* ## pom文件的继承关系
  * 可以通过为子module中的pom文件指定一个parent元素来指定它的父pom文件。此时通常要求父parent已经安装在本地仓库后者父pom文件位于子module pom文件的上一级目录。
    * 如果不满足上述条件，可以通过为parent元素指定relativePath来显式指定父pom文件的位置
  * 可以为父pom指定modules元素，并将父pom文件中的packaging元素指定为pom，此时对父module调用mvn命令时，相同的命令会被传递给子module执行
  * 子项目pom文件在继承父项目pom文件时，可以省略groupId和version，省略的内容会被自动填充为与父项目中pom文件定义的相同
* ## maven profile的激活方式
  * 通过命令行激活：可以在mvn命令中指定-P选项，并且在之后跟上profile的id名称来指明激活的profile，-P后跟随的profile将会和activeProfiles中指明的pofile一起被激活
  * 通过activition配置激活：在配置文件中，可以通过在profile标签中指定activition属性来指定激活条件
    * 可以通过jdk版本前缀选择是否激活：
    ```pom
    <!-- 
        jdk版本既可以详细指定，也可以通过范围来指定，比如
        <jdk>[1.8,17)</jdk>
        会检测jdk版本是否大于等于1.8并且小于17
     -->
    <profile>
      <activition>
        <jdk>1.8</jdk>
      </activition>
    </profile>
    ```
    * 可以通过os标签指定特定操作系统环境来选择是否激活
    * 可以通过property来通过系统属性判断是否激活，可以通过命令行-D来添加参数控制是否激活（系统环境变量可以通过env.XXX来获取）
    * 可以通过文件是否存在（file标签的exists和missing）来判断是否激活
  * activationByDefault：可以通过该标签来决定profile是否被激活
    *  但是如果在同一pom文件中，有其他profile被上述的激活方法激活，那么默认被激活的profile将不会被激活
    *  如果同一个pom文件中的其他profile通过命令行或者上述提到的其他方式被激活，那么所有默认激活的profile都不会被激活
   * deactive一个profile：可以通过-P !profilename来不激活一个profile
   * profile定义的位置：
     * 外部文件（位于pom文件之外的文件，例如settings.xml或者profiles.xml）只能定义repositories、pluginRepositories、properties属性
     * pom文件内
 * maven依赖管理
   * maven会自动包含可传递的依赖项，避免了需要手动指定项目依赖项所需要依赖的库。
   * 所有从父项目继承的依赖项，或者依赖项所需的依赖，都会在maven的项目中
   * 依赖中介：
     * 当项目中存在一个依赖的多个版本时，会选择在依赖树中距离当前项目最近的依赖所选择的版本。如果两个不同版本的依赖项距离当前的项目深度相同，那么第一个被声明的依赖项版本获胜。
     * 可以在项目中明确声明依赖项的版本来保证依赖项版本
   * 依赖管理：
     * 可以在dependencyManagement中添加依赖，添加后当发生依赖版本冲突或者没有指定依赖版本时，会使用dependencyManagement中定义的依赖版本
       * springboot在spring-boot-dependencies的pom文件中指定了一些依赖的默认版本，故而在构建springboot项目时一些依赖项并不用指定版本信息
     * 在dependencyManagement中，子项目声明的依赖会优于父项目声明的依赖，如果父项目和子项目同时在依赖管理中声明一个依赖的不同版本，那么以子项目声明的为准
     * 当发生版本冲突时，依赖管理优先于依赖中介，并不是看依赖距离当前项目的深度来决定版本，而是看依赖管理中定义的版本来决定
   * dependency scope：
     * 该机制可以允许maven只包含适用于当前阶段的依赖
     * dependency scope：
       * compile：默认的scope，在所有阶段的classpath中都可用
       * provided：声明为provided表明其只在编译和测试的时候被需要，而在运行时环境中classpath中并不包含该依赖，该依赖会在runtime中被提供
        * runtime：表明该依赖只在运行时环境中被需要，在runtime classpath中存在该依赖，但是并无法在compile时被访问
        * test：该依赖只会在测试时被使用，而在正式情况下不需要该依赖
        * system：类似于provided，但是必须要提供明确包含该依赖的jar包。该依赖必须一直可见并且不会再仓库中去查找。
          * system依赖要通过配置systemPath来指定该依赖的路径
        * import：仅用于dependencyManagement中的依赖，表示将当前依赖替换为目标pom文件中dependencyManagement中的内容
          * 当导入的依赖版本于当前依赖管理中自定义的依赖版本冲突时，以自定义的依赖版本为准
          * import通常用于解决只能继承一个父pom中依赖管理的问题，可以通过import来导入多个pom文件的依赖继承
          * 当import多个pom文件中的依赖管理时，如果发生了冲突，
            * 如果自定义的依赖管理项中包含冲突依赖，以自定义的版本为准
            * 如果自定义的依赖管理项中不包含冲突依赖，那么以最先被导入的版本为准
   * exclude依赖：
     * 可以在依赖中指定exclusion来排除某依赖
     * 使用场景：
       * 当项目的某个依赖项依赖了一个存在安全问题或者于jdk版本不兼容的依赖项，那么可以通过exclusion来排除该依赖项
       * 当对一个依赖项中的某个依赖进行排除时，不仅可以排除依赖项的直接依赖项，还可以排除该依赖项的传递依赖项
   * 可选依赖
     * 可以通过为依赖添加optional标签来将依赖声明为可选的
       * 如果依赖被声明为可选的，如项目B声明依赖A时可选的，那么当C依赖项目B时，C将不会包含依赖A，如果C要包含依赖A，需要显式的声明依赖A
       * 可选依赖的使用场景：
         * 如果库X2提供多种数据库的访问API，故而X2需要各种数据库的driver依赖，但是如果项目C依赖了X2，那么项目C仅需要一种数据库的driver。此时X2可以将各种driver声明为可选的，当项目C依赖X2时，仅需显式依赖于其选择的一种driver依赖即可