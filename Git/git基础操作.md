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
    ```
    * 如果修改已经被staged到staging area，如果想要将staged的修改变为unstaged，可以调用git reset命令
      ```shell
      # 通过git reset命令，可以取消staging area中对特定文件的修改，将
      #   staging area中特定文件同步到与commit提交记录相同
      #   但是工作区中的文件内容并不会被修改，除非指定--hard选项
      $ git reset HEAD filename
      ```
    * 如果想要放弃对文件的修改（未被staged），可以调用git checkout -- filename来文件内容进行恢复操作，此时，如果staging area中有对文件的修改，则被还原到staged的版本，如果staging area中没有对文件的修改，将被还原到最后一次提交的版本
    ```shell
    # git checkout -- filename 对工作区中的内容进行恢复
    #   该命令比较危险，可能会造成工作区中修改内容的丢失
    $ git checkout -- filename
    ```
    * 将已经被staged的修改unstaged，可以使用git restore --staged
      ```shell
      $ git restore --staged filename
      ```
    * 将对文件的修改恢复到上一次提交或是staged的状态
      ```shell
      # 将working directory中文件内容恢复到上一次提交或staged状态
      #   等同于git checkout -- filename
      $ git restore filename
      ```
  * ### git远程操作
    * 可以通过git remote 命令来查看远程仓库，并且用-v选项显示远程仓库的url
      ```shell
      # git remote -v显示本仓库中远程仓库的shortname和url地址
      $ git remote -v
      ```
    * 可以通过git remote add命令来添加远程仓库的信息
      ```shell
      # 添加远程仓库信息
      $ git remote add <shortname> <url>
      ```
    * 从远程仓库中获取数据，可以调用git fetch命令
      * git fetch命令会从远程仓库拉取数据到本地，在执行完此操作后，本地会拥有远程仓库的所有分支引用
      * 在调用完git clone <url>后，名为origin的仓库将会自动设置为url地址，用git fetch origin命令会自动拉取被提交到远程仓库的工作
      * git fetch只是将信息从远程仓库中下载下来，但是并不会自动的执行merge操作，也不会修改你working directory中的任何内容。你需要在之后手动的merge拉取数据
      ```shell
      # git fetch <remote>
      $ git fetch <remote>
      ```
    * git pull会将远程的分支拉取到本地，并且将会自动的把本地当前分支和远程分支合并
      * git clone在克隆远程仓库时，会自动的将本地的master分支设置为追踪远程仓库的默认分支。
    * git push将会将本地分支推送到远程仓库
      ```shell
      # git push会将本地分支推送到与本地分支关联的远程分支
      # 只有当对远程仓库具有写权限时，推送才能成功
      # 如果在fetch和push之间，其他用户对远程仓库执行了push操作，则当前push
      #   操作会被拒绝，你必须fetch它们的push并且将他们的push和本地相融合，
      #   然后再push，此时才能成功
      $ git push <remote> <branch>
    * 可以调用git remote show <remote repository>来显示remote的详细信息
      ```shell
      # git remote show <short name>会显示如下信息
      #   * 会显示在特定分支下调用git push时，本地分支会被push到哪个远程分支
      #   * 会显示在本地仓库还不存在的远程分支
      #   * 会显示本地仓库还存在，但是在远程仓库中被移除的分支
      #   * 会显示在调用git pull时哪些分支会自动的和远程分支相merge
      $ git remote show <shortname>
      ```
    * 可以调用git remote rename old-name new-name来对远程仓库进行重命名
      ```shell
      # 对远程仓库进行重命名
      $ git remote rename <old-name> <new-name>
      ```
    * 可以通过git remote rm命令来删除远程仓库
      ```shell
      # 删除远程仓库
      #   * 在删除远程仓库之后，和远程仓库相关联的信息（例如tracked remote 
      #       branch、远程仓库相关设置）都会被删除
      $ git remote rm <shortname>
      ```
  * ### git标签操作
    * 列出当前git仓库中的标记
      * 可以通过git tag来列出标记，但是列出的标记会按字母的顺序显示
        ```shell
        # 按字母顺序列出标签
        $ git tag
        ```
      * 按照给出的模式查找特定的标签
        ```shell
        # 通过为git tag指定-l选项查找特定的标签
        $ git tag -l "v1.25.3*"
        ```
    * tag分类
      * 带注释的标签：
        * 对于带注释的标签，可以通过git tag -a version_name -m comment来指明
        ```shell
        # 创建带注释的标签
        $ git tag -a <tag-name> -m <comment>
        ```
        * 如果要查看标签的信息，可以通过git show命令
        ```shell
        # 查看标签的内容
        $ git show <tag-name>
        ```
      * 轻量级标签：
        * 创建轻量级标签，不需要指定-a或者-m，只需要指明标签名就行
          ```shell
          # 创建轻量级标签
          $ git tag <tag-name>
          ```
        * 对于轻量级标签，调用git show并不会显示标签附加的注释或是tagger和标记时间，而只会显示标记的commit信息
          ```shell
          # 显示轻量级标签信息
          $ git show <tag-name>
          ```
    * 对git log中之前的提交添加tag
      * 对于非当前commit，也可以用git tag来为其添加标签，只需指明commit的checksum值（可以是部分checksum值）
      ```shell
      # 为git log中之前的提交指明tag
      $ git tag -a <tag-name> -m <comment> <commit-checksum>
      ```
    * 向远程库中push标签
      * 默认情况下，git push命令并不会将标签推送到remote repository，需要显式的调用git push origin tag-name来推送标签
      ```shell
      # 将本地git仓库中的标签push到远程仓库
      $ git push <shortname> <tag-name>
      ```
      * 将本地所有的标签都推送到远程仓库，可以指定--tags选项
      ```shell
      # 推送本地所有标签到远程仓库
      #  * 如果指定了--tags选项，那么轻量级和带注释标签都会被推送到remote
      #  * 如果指定的是--follow-tags选项，只会推送带注释标签
      #  * 目前无法只推送轻量级标签
      $ git push <shortname> --tags
      ```
    * 删除本地标签
      * 如果想要删除本地仓库中的标签，可以通过git tag -d来实现
      ```shell
      # 删除本地仓库中的标签
      $ git tag -d <tag-name>
      ```
      * 如果想要删除远程库中的标签，可以在git push中指定--delete选项
      ```shell
      # 删除远程库中的标签
      $ git push <shortname> --delete <tag-name>
      ```
    * 通过调用git checkout tag-name，将会进入detached head state，此状态下可以执行提交操作，但是提交的操作是不可达的。如果想要对旧的版本进行修改，可以创建一个新的分支，并且在新的分支上对修改进行提交
    ```shell
    # 在新的分支上对旧的tag版本进行修改
    $ git checkout -b <branch-name> <tag-name>
    ```
  * ### git 别名
    * 可以通过git config --global来为git操作定义别名
      ```shell
      # 例如，想要为文件的恢复操作定义一个别名，可以调用如下命令
      $ git config --global alias.unstage resotre --unstage
      or
      $ git config --global alias.unstage reset HEAD -- 
