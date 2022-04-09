# CMake教程

* ## CMake的跨平台特性：
    * 由于在源文件规模较大时，每次编译都重复输入gcc命令并指定源文件是很繁重的工作，为了解决该问题，通过 makefile文件指定每次编译过程的流程和clean等环节的命令，可以只需输入make命令就完成一系列构建的工作。  
      <br/>
    * 
  但是，多个平台下make工具的格式并不都相同，如windows平台下的nmake、QT平台下的qmake等等，为了避免在不同平台重复编写不同格式的qmake，可以通过编写CMakeLists.txt文件，并根据不同平台的需要将其转化为对应平台下的make格式。  
  <br/>

* ## CMake中的指令：
    * ***cmake_minimum_version(VERSION 版本号):***<br/>当前项目需要的最小项目版本  
      <br/>
    * ***project(项目名)：***<br/>当前项目的名称  
      <br/>
    * ***add_executable(可执行文件 source1 source2...):***<br/>生成的可执行文件名称和其依赖的源文件  
      <br/>
    * ***set(CMAKE_CXX_STANDARD 11/14):***<br/>将c++标准设置为11或者14  
      <br/>
    * ***set(CMAKE_CXX_STANDARD_REQUIRED On/Off):***<br/>
      如果CMAKE_CXX_STANDARD_REQUIRED设置为True，则必须使用该c++版本，若设置为Off，可以在不支持该版本的情况下选用其他版本  
      <br/>
    * ***add_library(库名称 [STATIC|SHARED] source1 [source2...]):***<br/>
      用指定的源文件构建动态/静态库，当未指定STATIC/SHARED时，默认会生成静态库.a文件，指定static后生成.so动态库文件  
      <br/>
    * ***target_include_directories(可执行文件名 PUBLIC 头文件目录...):***<br/>添加头文件  
      <br/>
    * ***target_link_libraries(可执行文件名 PUBLIC 库名称...):***<br/>添加库
  