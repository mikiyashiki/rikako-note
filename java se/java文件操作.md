# Java文件操作  

* ## Path路径
  * path路径可分为绝对路径和相对路径，绝对路径从根路径开始，而相对路径则不是
  * Path类的api如下：
    ```java
    /*
    *  获取路径，其会将参数中的给定字符串连接起来，创建路径
    *  如果不是文件系统中的合法路径，会抛出InvalidPathException
    *  Paths.get并不要求文件系统中一定存在对应的文件
    */
    Paths.get(String first,String... more)

    // 解析路径
    // 如果path2是绝对路径，则会返回path2
    // 否则，以path1为基本路径，返回path2想对于path1产生的路径
    path1.resolve(path2)
    path1.resolve(String)

    // 将path1父路径作为基本路径解path2的析兄弟路径
    path1.resolveSibling(path2)
    path1.resolveSibling(String)

    // 以path1为基本路径，返回path2想对于path1的相对路径
    path1.relativize(path2)

    // 移除.和..等多余元素
    path1.toAbsolutePath()

    // 获取文件名称
    path1.getFileName()

    // 获取根路径
    path1.getRoot()

    // 获取父路径，如果当前文件没有父路径，返回null
    path1.getParent()

    // 以当前路径创建元素
    path1.toFile()

    # 上述操作都不要求path所对应的文件在文件系统中已经存在
    
    ```

* ## 文件读写
  * Files类相关文件操作的api
    ```java
    // 读取路径所对应文件的所有字节
    Files.readAllBytes(path)

    // 按行读入文件的内容，并且将文件的行保存在字符串的集合中
    Files.readAllLines(path,charset)

    // 向文件中写入数据
    // 可以通过StandardOpenOpition来指定数据是append还是覆盖
    Files.write(path,bytes,charset,StandardOpenOpition.xxx)
    Files.write(path,lines,charset,StandardOpenOption.xxx)

    // 通过FIles类获取输入输出流
    Files.newInputStream(path,options)
    Files.newOutputStream(path,options)
    FIles.newBufferedReader(path,charset)
    Files.newBufferedWriter(path,charset,options)


    ```

* ## 创建文件和目录
  ```java
    // 通过Files类创建目录
    //      其中，createDirectory方法中path路径中的中间目录都应该已
    //              存在，而且当想要创建的目录已经存在时会抛出异常
    //      而createDirectories方法中中间路径可以不存在，在通过path创
    //              建目录时会联通中间路径一起创建，但是，此方法在想要创
    //              建的目录已经存在时不会抛出异常
    Files.createDirectory(path)
    Files.createDirectories(path)
    # 注意，其中createDirecotry(path)操作是原子的
    # 但是createDirectories()不是原子的，多进程条件下可能存在竞争

    // 通过FIles类创建文件
    // 当文件已经存在时，会抛出一场
    // 该创建文件的操作是原子的
    Files.createFile(path)

    // 创建临时文件和临时文件夹
    Files.createTempFile(basedir,prefix,suffix)
    FIles.createTempFile(prefix,suffix)
    Files.createTempDirecotry(basedir,prefix)
    FIles.createTempDirectory(prefix)

    # 可以为生成的临时文件调用deleteOnExit方法来让临时文件在进程退出时删除
    # 在linux环境下，临时文件和目录生成在/tmp目录下

  ```

* ## 移动、复制、删除文件
   ```java
    // 移动、复制文件
    Files.copy(from,to)
    FIles.move(from,to)

    # 对于move和copy操作，如果目标路径已经存在，那么操作将失败并抛出异常
    # 可以指定REPLACE_EXISTING来覆盖已经存在的路径
    # 默认情况下，移动和复制都不是原子的，可以通过ATOMIC_MOVE来指定移动操
    #       作是原子的，但是复制操作并不支持原子性操作
    # 可以将输入流中的内容复制到path对应的文件中，也可以将path对应文件的
    #       内容输出到流中

    // 删除文件
    // 若该文件不存在，会抛出异常
    // 删除操作并不是原子的，由于要检查该文件是否是目录
    // 如果文件是目录，只有当该目录为空时才能删除成功
    Files.delete(path)

   ```

* ## 获取文件属性
    ```java
    // 文件是否存在
    Files.exists()
    // 文件是否是隐藏文件爱你
    Files.isHidden()
    // 对文件是否有读写执行权限
    Files.isReadable()
    Files.isWritable()
    FIles.isExecutable()
    // 文件类型是否是目录、普通文件、符号链接
    Files.isRegularFIle()
    FIles.isDirectory()
    FIles.isSymbolicLink()
    // 获取文件长度
    FIles.size()

    ```

* ## 访问目录中的项
    ```java
        // 将目录中的项以Stream的形式返回
        // Files.list并不会递归的列出子目录中的项
        Files.list(dirPath)

        // 递归列出子目录的项
        // 可以通过指出depth来指定遍历的深度
        FIles.walk(rootPath,depth)
    ```