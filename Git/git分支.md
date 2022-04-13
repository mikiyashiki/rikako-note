# Git Branch
* ## Git原理
  * 在git中，并不通过修改集或者差异来存储数据，而是按照快照的方式来存储数据。
    * 当在执行一次commit时，git会存储commit object。commit object中包含了一个指向快照的指针。
    * commit object包含如下信息：
      * 作者名称和email地址
      * 为commit指定的提交信息（comment）
      * 指向快照（snapshot）的指针
      * 指向之前提交的指针（parent或parents）：
        * 对于正常的提交，有0个（init commit）或者一个parent
        * 对于合并多个分支的commit，有多个parent
    * git 提交的具体细节：
      * 对于将文件stage的操作，git会为每个staged的文件计算一个checksum，并且将文件的版本存储到git仓库中（文件版本将会被当做blob访问），并且将checksum添加到staging area中
      * 当调用git commit时，git为项目的每个子目录计算checksum，，并且将它们作为树形对象存储到git仓库中。
      * git稍后会创建一个commit object，commit object中包含元数据和指向项目树对象的指针，树结构中指明了目录下文件指向哪个blob。由于快照只是指向树对象的指针，故而快照可以被重复创建。
      * snapshot会包含一个指向它前面快照的指针
    * git branch的本质：
      * 在git中，git branch实际上只是一个指向commmit的可移动指针。
      * 对于每次提交，master branch的指针都会自动移动向最新的提交。
      * HEAD指针：
        * HEAD指针指向当前所在的分支，可以通过git log指令查看HEAD指针当前和哪些分支的指针相同
* ## Git Branch Operation
  * 创建新的branch
    * 创建新的分支实际上是创建了一个可供移动的新指针，指向了当前所在的提交（HEAD指针所指向的提交）
    * 可以通过git branch来创建一个新的分支
      ```shell
      # 创建一个指向当前快照的新branch指针
      $ git branch <branch-name>
      or
      # 创建一个新分支并且移动到该分支
      $ git checkout -b <new-branch-name>
      # 创建一个新分支并移动到该分支
      $ git switch -c <new-branch-name>
      ```
  * 在现有的分支之间进行切换
    * 在分支之间进行切换实际上是把HEAD指针指向别的branch指针
    * 可以通过git checkout来实现分支之间的切换
      * 在调用git checkout在分支之间进行切换时，工作目录中的内容也会被还原到目标分支的版本，故而在切换之前需要对尚未提交的任务进行提交，否则未保存的任务会丢失
      ```shell
      # 在分支之间进行切换
      #  * 在切换分支时，会做如下事情
      #     * 将HEAD指针指向想要切换到的分支
      #     * 将working directory中的文件还原到目标分支的快照状态
      $ git checkout <branch-name>
      or
      $ git switch <branch-name>
      # 移动到上一个分支
      $ git switch -
      ```
  * 在分支下调用git log
    * git log只会显示当前分支下的提交历史，不会显示其他分支下的提交历史。
      * 如果要显示指定分支的提交历史，为git log指定branch-name
        ```shell
        $ git log <branch-name>
        ```
      * 如果想要显示所有分支下的提交历史，可以为git log添加-all选项
        ```shell
        $ git log --all
        ```
  * 显示git分支状态图
    * 通过为git log指定--graph选项，可以输出git分支的状态图
      ```shell
      $ git log --all --pretty=oneline --graph
      ```
  * 合并分支
    * 可以通过git merge操作来合并两个分支
      * 如果想要让当前分支合并一个分支，并且当前分支对于目标分支来说在commit history中是可达的，那么git会直接让当前分支的指针移动到目标分支的最后一次提交位置。这被称之为“fast-forward”机制。
      ```shell
      # 可以通过git merge命令来对分支进行合并
      # 如果合并的目标分支的git log记录中当前分支是可达的，那么
      #     会直接将当前分支的指针移动到目标分支指向的位置
      $ git merge <branch-name>
      ```
      * 如果当前分支的last commit并不是目标分支的祖先，那么合并分支需要通过创建一个新的snapshot，新的snapshot存储合并分支的结果，并且创建一个commit指向snapshot。新的commit具有两个parent。
        * 在合并后，如果被合并的分支也不再被需要，那么同样可以通过git branch -d操作来删除旧的分支
  * 删除不再被需要的分支
    * 通常，当创建一个hotfix分支来修复bug时，如果bug已经被修复，切换到master分支进行merge操作后，hotfix分支不再被需要，可以调用git branch -d来删除分支
      ```shell
      # 删除不再被需要的分支
      #   * 当删除已经被合并的分支时，可使用-d选项
      #   * 如果待删除分支没有被合并，那么需要指定-D选项来强制删除
      $ git branch -d <branch-name>
      ```
  * 分支合并冲突
    * 在合并时，相较于两个分支的共同祖先，如果两个分支修改了同一个文件的同一部分，那么文件的合并会发生冲突。
    * 在发生合并冲突后，可以通过git status来检测冲突的细节
      ```shell
      # 通过git status来检测合并冲突的细节
      $ git status
      ```
  * git分支管理
    * 查看当前项目的分支，可以用git branch命令
      ```shell
      # 查看当前项目的分支
      $ git branch
      or
      # 查看当前项目的分支，并且列出每个分支的最后一次提交
      $ git branch -v
      # 可以通过--merged或者--no-merged选项来对已经或者还未合并到当前
      #     分支的分支进行过滤
      # 列出已经合并到当前分支的分支
      $ git branch --merged
      # 列出尚未被合并到当前分支的分支
      $ git branch --no-merged
      ```
      * 对于git branch --merged列出的分支，除了当前分支外都可以删除，因为已经对其进行了合并操作，可以无丢失的删除该类分支
    * 对分支进行重命名
      * 可以通过git branch --move来对本地分支进行重命名
      ```shell
      # 重命名本地分支
      $ git branch --move <old-branch-name> <new-branch-name>
      ```
      * 对远程仓库的分支进行重命名操作
        * 可以通过git push --set-upstream origin new-name来对远程仓库的分支进行重命名操作
          * 执行该操作后，远程仓库中旧分支名仍然会存在，需要手动的删除
        ```shell
        # 重命名远程分支
        $ git push --set-upstream <remote> <new-name>
        # 删除旧分支
        $ git push <remote> --delete <old-name>
* ## 远程分支
   * 远程tracking分支
     * 远程tracking分支是对远程分支状态的引用。远程tracking分支虽然是本地分支，但是你并不能对其进行移动。只有每次当你与服务器进行网络连接时，远程tracking分支才会移动。
     * 远程tracking分支名字按照remote/branch-name的形式组成
     * 在调用git clone方法时，会自动创建一个origin/master的远程tracking分支和一个本地的master分支。origin/master分支在之后没有和服务器通信的时间段内都不会被修改。
       * 当调用git fetch origin操作后，位于服务端的信息被拉取到本地，并且origin/master也会被移动到和服务端相同的位置，此时origin/master和本地master分支进行合并可能会出现冲突的问题
     * 如果有多个远程项目，那么属于不同项目的远程tracking分支通过remote shortname区分
      ```
      # remote tracking branch in different remote server
      #   * such as remote-1/ProjectName
      #   *              remote-2/ProjectName
      ```
    * 将本地分支推送到远程仓库
      * 可以用git push操作将本地分支推送到远程分支
      ```shell
      # 推送本地分支
      $ git push <remote> <local-branch-name:remote-branch-name>
      # 如果远程分支名称和本地分支相同，那么可以简化为如下形式
      $ git push <remote> <branch-name>
    * 将远程分支拉取到本地
      * 当调用git fetch方法将远程仓库拉取到本地时，对于远程的分支hotfix，拉取到本地后只会有一个remote/hotfix的远程tracking分支，并不会创建一个名为hotfix的本地分支。
      * 如果想要基于远程tracking分支的基础上创建一个本地分支，可以调用如下命令
      ```shell
      # 创建一个本地分支，并且将开始位置设置为remote/branch
      $ git checkout -b 'branch-name' remote/branch
      or
      $ git switch -c 'branch-name' remote/branch
      ```
    * 跟踪分支
      * 根据远程tracking分支创建分支时，会自动的创建一个跟踪分支。
      * 跟踪分支是一个和远程分支相关联的本地分支，当位于跟踪分支时，调用git pull时，会自动知道应该从哪个远程服务器进行拉取，并且知道整合哪个分支
      * 当调用git clone时，会自动创建一个本地的跟踪分支master，master分支和远程分支origin/master分支相关联
      * 创建一个跟踪分支，除了使用git checkout -b 'branch-name' 'remote/branch'外，还可以使用--track来指定
        ```shell
        # 创建跟踪分支并且切换到该分支
        $ git checkout --track remote/branch

        # 甚至，当checkout指定的名称
        #   * 本地仓库中并不存在该名字的分支
        #   * 该名字的分支在远程仓库中存在
        # 那么，调用git checkout <branch-name>就会直接创建跟踪分支
        $ git checkout <branch-name>
        ```
      * 如果已经存在一个本地分支，并且想将其跟踪远程分支；或者想要对一个本地分支跟踪的远程分支进行修改，可以使用git branch -u来完成
        ```shell
        # 添加或者修改本地分支绑定到的远程分支
        #   * 通过指定-u选项或者--set-upstream-to选项
        $ git branch -u remote/branch [local-branch]
        or
        $ git branch --set-upstream-to remote/branch [local-branch]
        ```
      * 查看各个本地分支绑定的远程分支
        * 在git remote show中查看
          ```shell
          # 通过git remote show查看
          $ git remote show <remote-name>
          ```
        * 通过git branch -vv查看
          ```shell
          # 通过git branch -vv查看
          # * git branch -vv并不会连接服务器，而是会从上次连接服务器缓存的
          #     数据中读取
          # * 如果想要获得实时的值，在执行该命令前最好加上git fetch --all
          $ git branch -vv
          ```
      * git pull和git fetch区别
        * git fetch只是从远程服务端拉取数据，但是并不会自动合并，需要自己手动合并
        * git pull，从服务端拉取数据后会自动合并
      * git删除远程仓库的分支
        * 可以通过git push remote --delete branch来删除远程的分支
          ```shell
          # 删除位于远程服务端的分支
          $ git push <remote> --delete <branch-name>
          ```
* ## Git Rebasing
  * 和merge操作不同，对于rebase操作，如果想把A快照基于B快照rebase，此时会找到A和B的最近公共祖先快照，并且把快照A基于祖先快照的修改记录下来，并且将该修改基于快照B再进行执行产生一个新的快照C，并且将C的parent设置为B。之后会将指向快照B的指针通过fast-forward机制修改指向快照C。
  * 相对于merge操作，rebase操作产生的结果和merge操作合并后快照的结果完全相同。但是，相对于merge操作，rebase操作所产生的git log是一条直线，其 历史会更加的简洁。
  * git rebase操作：
    ```shell
    # git rebase <target-branch>
    # 其会找到当前分支和目标分支的公共祖先快照，并且将当前分支的快照
    #   相对于公共祖先的快照变化基于target分支的最后提交replay
    #   然后将当前分支的节点指向到产生的新快照
    $ git rebase <target-branch>
    ```
    * 在调用git rebase时，和git merge一样，可能会出现冲突的问题。故而，在出现冲突后，需要手动解决冲突，并且调用git rebase --continue命令。
  * git rebase复杂操作
    ```shell
    # 调用git rebase，可以将一个分支基于另一个分支的改变replay到第三分支上
    $ git rebase --onto target-branch base-branch topic-branch
    ```
  * 相较于merge，rebase操作可能会产生一些问题。
    * 如一个分支已经被push到远程库，且被其他协作者pull并在此基础之上进行开发工作。那么，当分支的创建者在本地对分支进行rebase操作后，再用git push --force命令对远程分支的history进行覆盖时，那么其他协作者对更新之后的远程库进行pull操作，merge操作会破坏history
  * rebase操作的守则：
    * 一个rebase操作只能再本地库进行
    * 一旦分支被push到远程库，就有可能被其他开发者clone并在此基础之上进行修改
    * 如果分支被push到远程库后，那么就无法再进行rebase操作。因为rebase操作可能会破坏已有的history结构，如果用git push --force强制覆盖remote仓库的hsitory结构，那么其他开发者再次pull的时候就会进一步破坏history结构