# Git
* ## Git基本操作
  * ### 获得一个git仓库，可通过如下方式：
    * 将并未使用版本控制的本地目录转化为git仓库
      * 在本地目录下，可以通过git init来将当前目录置于git的版本控制管理之下
    * 从远程（比如GitHub）克隆一个git仓库
        ```shell
        > git init
        ```
      * git clone [dirname] 可以将git项目从远程拷贝到本地
        ```shell
        > git clone $url
        ```
  * ### 记录git仓库的变化
    * git仓库中文件的状态：
      * tracked：处于tracked状态的文件是在最后一次提交的快照中存在的文件，git仓库知道这些文件的存在。这些文件可以是modified、unmodified、staged
      * untracked：处于untracked状态的文件，在最后一次提交的快照中并不存在该文件，而且该文件也并不属于unstaged状态，
    * git文件状态变化：
      * 当一个文件处于untracked状态时，要将对该文件调用git add状态将其变为tracked状态，并且要通过git commit命令将变化提交，此时文件会变为unmodified状态
      * 可以通过git status来查看当前git仓库中各个文件的状态
        ```shell
        # 查看git仓库的信息
        > git status
        ```
    * 跟踪git仓库中文件状态：
      * git add命令可以将文件从untracked状态变为tracked状态，并且能将对文件的修改从unstaged状态变为staged状态
        ```shell
        # 如果参数后跟的是目录名称，那么会递归的将目录中所有的文件都标记为staged状态
        > git add [文件名/目录名]
        ```
      * 在对文件进行修改之后，必须调用git add将修改变为staged状态，否则，在修改后直接调用git commit，提交的仍然是上次调用git add时的文件状态。
      * 可以通过为git status添加更多选项来指定输出：
        ```shell
        # 为git status指定 -s或者 --short选项，会用字母来表示各个文件的状态
        #   ？？ 表示文件为untracked状态
        #   A      表示被添加到staging area的新文件
        #  M      表示文件被修改过但是没有被staged
        #  AM    表示该文件在add之后又被修改过
        > git status  -s
        ```
    * 如果想要git仓库不跟踪一些文件的文件状态（例如日志文件、由系统产生的文件等），在调用git status时也不希望该类文件被列出，可以通过在目录下指定一个.gitignore文件，并且在文件中指明被忽略的文件的模式
    ```shell
    # .gitignore文件语法
    #   空行或者以'#'开始的行将会被忽略
    #   模式会递归的应用到文件树中的所有文件
    #   可以在模式开始之前加上/符号来避免递归
    #   可以将模式以/结尾来指定一个目录
    #   可以通过！表示明确追踪某个模式的文件（例如，如果确定忽略*.a模式
    #       的文件，但是只想追踪liba.a文件，可以指定模式为
    #           *.a
    #           !liba.a
    #   可以通过**来匹配嵌套的目录，如a/**/b，既可以匹配a/b，也可以
    #       匹配a/x/y/b和a/x/b
    $ cat .gitignore
    *.[oa]
    *~
    ```
    * 查看git仓库中文件变化的具体细节
      * 查看仓库中文件变化的具体细节，有如下两类查看：
        * 查看已经修改但是并没有被staged的修改：
        ```shell
        # 该命令会比较work directory和staging area的内容，并且输出具体
        #       哪些内容没有被staged
        >  git diff
        ```
        * 查看仓库中文件已经被staged但是没有被commit的变化：
        ```shell
        # 该命令会比较staging area中的内容和上次提交的内容，并且输出
        #       具体的差异内容
        $   git diff --staged
        ```
    * git提交：
      * 可以通过git commit命令来提交staging area中的内容
        ```shell
        # 通过git commit可以提交已经被staged的修改，但是，已经修改但是
        #       没有被staged的修改并不会被commit
        #  可以通过git commit -a来省略git add的过程，其会自动对已经traked
        #       的文件调用git add操作
        $   git commit [ -a ]
        ```
    * 可以通过git rm来将文件从tracked files中移除，并且，工作目录中的该文件也会被移除
      * 如果该文件已经被修改或是已经将修改提交到staging area中，则git rm方法必须要指定-f选项强制删除（避免错误调用git rm而导致处于staged状态但是并未被提交的修改或者还未被staged的修改丢失，需要强制指定-f）
      * 如果想要将文件从staging area中删除，但是不将其从文件系统中删除，可以为git rm命令指定--cached选项
        ```shell
        #   --cached选项通常用于.gitignore文件中遗漏文件被git add命令提
        #       交到staging area的情况，此时可以通过git rm --cached命令
        #       将提交的文件修改从staging area中移除
        ```
    * git mv：可以通过git mv来对文件进行重命名
  * ### 查看git的提交记录
    * 通过git log可以查看该git仓库的提交记录，记录按照时间顺序逆序排列。
      ```shell
      # git log 按照时间顺序逆序显示提交记录的顺序
      $ git log
      ```
    * 通过为git log指定-p(--path)选项，可以显示输出每次commit相对于上次commit快照的区别
      ```shell
      # git log -p可以输出每次commit相对于上一次commit引入的变化
      #   额外指定-n(例如-1，-2)可以指明输出的记录条数
    * 为git log指定--stat选项查看文件修改的统计数据（例如有多少个文件被修改，每个文件插入或者删除多少行）
      ```shell
      # git log --stat 可以显示每个文件修改的统计信息
      $ git log --stat
      ```
    * 为git log命令指定--pretty选项可以将git log的输出格式转为默认值以外的其他值
      ```shell
      # --pretty选项有一些预先定义好的值：
      #   --pretty=oneline：
      #     将每条记录的输出结果格式化为一行，适用于输出多个提交的情况
      #   --pretty=short
      #      显示格式和git log类似，但是不包含Date
      #   --pretty=full
      #       显示结果和git log类似，包含Author和Commit
      #   --pretty=fuller
      #       显示结果类似于git log，包含Author、Commit、
      #             AuthorDate、CommitDate
      $ git log --pretty=oneline
    * 可以通过--pretty=format:"format string"来自定义输出的格式
      ```shell
      # 自定义输出格式
      $ git log --pretty=format:"format string"
      ```
    * 通过--graph选项，可以输出ascii码组成的图片，显示分支合并的过程
      ```shell
      # 通过--graph选项显示分支合并过程
      $ git log --graph
      ```
    * 输出某一时间范围内提交的修改，可以为git log指定--since和--until选项
      ```shell
      # --since选项支持多种格式，可以指定时间范围的起始日期
      # --until选项指定时间范围的截至日期
      $ git log --since="3 days ago 13:00" --until="today 13:00"
      ```
    * 可以通过--author、--committer、--grep来过滤提交记录
      ```shell
      # --author="author name":通过作者名称过滤提交记录
      # --committer="committer name":通过提交者名称过滤提交记录
      # --grep="grep pattern"
      # --grep和--author都可以指定多个，输出记录满足任意一个即可
      #     而可以通过--all-match可以指定只输出满足全部--grep的记录
      $ git log --author="Rikako Wu" --grep="daily" --author="Kitahara Kazusa"
    * 可以通过-S选项过滤修改了特定字符串的提交记录
      ```shell
      # 通过-S选项，可以过滤输出特定字符串的出现次数被改变的提交记录
      #   例如：某次修改添加或删除、修改了某个字符串
      #       则该字符串的出现次数在两次提交中会发生改变
      $ git log -S "dart"
      ```
    * 通过-- filename输出指定文件或目录在提交中被改变的记录
      ```shell
      # 通过-- dirname/filename
      #   只有当目录或文件在提交中被改变，才会输出该提交记录
      $ git log -- filename/dirname
      ```
  * ### 在git中执行undo操作
    * 修改最后一次提交：
      * 如果在最后一次提交中忘记了git add某次修改就进行提交，那么为了对上次的提交进行修改，可以用git commit --amend来覆盖上次的提交
    ```shell
    # git commit --amend选项会弹出上次的提交并且将修改后的提交插入到仓库中
    $ git commit //上次提交
    $ git add . //对忘记的文件进行add操作
    $ git commit --amend //覆盖上次的提交