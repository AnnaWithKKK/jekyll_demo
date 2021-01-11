# Git

Git 属于分散型版本管理系统，是为版本管理而设计的软件。

版本管理就是管理更新的历史记录。如记录一款软件添加或更改源代码的过程，回滚到特定阶段，恢复误删除的文件等。

## 1.基本操作

- 设置姓名和邮箱地址

  使用 Git 时，先设置姓名和邮箱，这两个信息将包含在 Git 的提交日志里，在 GitHub 上公开仓库时，也会随着提交日志一起公开，注意不要使用不便公开的隐私信息。

  ```shell
  $ git config --global user.name "Firstname Lastname"
  $ git config --global user.email "your_email@example.com"
  ```

  以上命令将会在`~/.gitconfig`中以下形式输入设置文件

  ```shell
  [user]
  	name = Firstname Lastname
  	email = your_email@example.com
  ```

  可以直接编辑配置文件，更改信息。

  同时，可以使用以下命令提高命令输出的可读性

  ```shell
  $ git config --global color.ui auto
  ```

  `~/.gitconfig`将增加一行

  ```shell
  [color]
  	ui = color
  ```

  

- 设置 SSH Key

  GitHub 上连接已有仓库，是通过使用 SSH 公开密钥来进行认证的。

  ```shell
  $ ssh-keygen -t rsa -C "your_email@example.com"
  Generating public/private rsa key pair.
  Enter file in which to save the key
  (Users/your_user_directory/.ssh/id_rsa): (press Enter)
  Enter passphrase (empty for no passphrase): (input passphrase)
  Enter same passphrase again:
  ```

  将 id_rsa.pub 公开密钥添加至 GitHub - accounting settings - add SSH key，就可以利用 private key 与 GitHub 进行认证和通信了。

- 初始化仓库

  使用 Git 进行版本管理，首先得初始化仓库

  ```shell
  $ mkdir git-tutorial
  $ cd git-tutorial
  $ git init
  ```

  初始化仓库后，目录下生成`.git`目录，该目录存储管理当前目录文件所需的仓库数据。

  在 Git 中，一般把这个目录的内容称为“附属于该仓库的工作区”。文件的编辑等操作在工作区中进行，然后记录到仓库中，以此管理文件的历史快照。

- 查看仓库状态

  ```shell
  $ git status
  ```

  

- 向暂存区添加文件

  ```shell
  $ git add 
  ```

  如果只是用 Git 仓库的工作区创建了文件，那么该文件并不会被记入 Git 仓库的版本管理对象中。要想让文件成为 Git 仓库的管理对象，就需要用 `git add` 将其加入暂存区（Stage 或 Index），暂存区是提交之前的一个临时区域。

- 丢弃修改的文件

  ```shell
  # 修改后不想 add， 直接丢弃
  $ git checkout -- file
  
  # 修改后已经 add， 尚未 commit,使用 reset HEAD 将暂存区的修改撤销（unstage）
  $ git reset HEAD file
  # 再丢弃工作区的修改
  $ git checkout -- file
  
  # 已经 commit？ 只有 reset --hard commit_id 一条路了
  ```

  

- 保存仓库的历史记录

  ```shell
  $ git commit -m ""
  ```

  `git commit` 可以将当前暂存区的文件实际保存到仓库的历史记录。通过这些记录，可以方便的在工作区中复原文件。

- 查看提交日志

  ```shell
  $ git log
  
  # 只显示提交信息的第一行
  $ git log --pretty=short
  
  # 只显示指定目录、文件的日志
  $ git log README.md
  
  # 显示文件的改动
  $ git log -p
  # or
  $ git log -p README.md
  ```

  

- 查看更改前后的差别

  `git diff` 可以查看工作区、暂存区、最新提交的差别

  ```shell
  $ git diff
  # or 查看和最新提交的差别
  $ git diff HEAD
  
  # 查看工作区和版本库最新版本区别, readme.txt for example.
  $ git diff HEAD --readme.txt
  ```

- 删除文件

  ```shell
  # 直接 rm 删除文件
  # git 确认从版本库删除
  $ git rm file
  # 删除了，从版本库恢复，但只能回复最新提交版本库修改的内容
  $ git checkout -- file
  ```

  

  

- 添加远程仓库

  ```shell
  $ git remote add origin git@github.com:github-book/git-tutorial.git
  ```

- 查看远程库信息

  ```shell
  $ git remote
  # or 显示更详细信息
  $ git remote -v
  ```

  

- 推送远程仓库

  不一定将所有本地分支都远程推送，`master` 分支主分支，要时刻与远程同步；`dev` 为开发分支，团队所有成员都需要在上面工作，也需要与远程同步；`bug` 分支只用于在本地修复bug，没啥必要，除非老板要统计修复bug数；`feature` 分支是否推送远程，取决于是否需要团队合作开发。

  ~~~shell
  # 当前分支下本地仓库推送到远程仓库, -u 在推送的同时，将 origin 仓库的 master 分支设置为本地仓库当前分支的上游，也就是说，将来运行 git pull 从远程仓库获取内容时，本地仓库的这个分支可以直接从 origin 的 master 分支获取内容，无需另外添加参数
  $ git push -u origin master
  
  # 推送至远程分支仓库
  $ git checkout -b feature-D
  $ git push -u origin feature-D
  
  ~~~

  

- 获取远程仓库

  ```shell
  # 获取远程仓库
  $ git clone git@github.com:github-book/git-tutorial.git
  
  # 查看当前分支的信息
  $ git branch -a
  
  # 获取远程的 feature-D 分支
  $ git checkout -b feature-D origin/feature-D
  
  # 获取最新的远程仓库分支
  $ git pull origin feature-D
  ```

  

## 2.分支操作

在进行多个并行作业时，往往同时存在多个最新代码状态，这时会用到分支。

不同分支中，可以同时进行完全不同的作业。等该分支的作业完成后再与`master`分支合并。

- 分支管理策略

  通常合并分支时，Git 默认用 `Fast forward` 模式。这种模式下，删除分支后，会丢掉分支信息。

  如果强制禁用 `Fast forward` 模式，Git 会在 merge 时生成一个 commit，这样，从分支历史上就可以看出分支信息。

  ```shell
  # for test
  $ git switch -c dev
  $ git add readme.txt
  $ git commit -m "add merge"
  $ git switch master
  # --no-ff 用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而 fast forward 合并就看不出来曾经做过合并
  $ git merge --no-ff -m "merge with no-ff" dev
  $ git log --graph --pretty=oneline --abbrev-commit
  ```

  

- 分支策略

  实际干活中，应按以下原则分支管理：

  首先，`master` 分支应稳定，仅用来发布新版本，平时不能在上面干活。

  干活都在 `dev` 分支上，`dev` 分支是不稳定的，到某个时候，比如 1.0 版本发布时，再把 `dev` 分支合并到 `master` 上，在 `master` 分支发布 1.0 版本。

- 显示分支一览表

  ```shell
  # 将分支名列表显示，同时可以确认当前所在分支(前面 * 表示)
  $ git branch
  ```

  

- 创建、切换分支

  ```shell
  # 切换到 feature-A 分支并进行提交
  $ git checkout -b feature-A
  # or 
  $ git branch feature-A
  $ git checkout feature-A
  
  # 切换到 master 分支
  $ git checkout master
  
  # 切换回上一个分支
  $ git checkout -
  ```

- bug 分支

  `dev` 工作时，临时需要修复 bug。

  ```shell
  $ git status
  # 还没提交呢，咋办？ 暂存下，将当前工作现场储藏，之后再恢复继续工作
  $ git stash
  Saved working directory and index state WIP on dev: f52c633 add merge
  
  # 此时，git status 确定 工作区干净的，放心创建分支修复 bug 了
  # 假定在 master 分支上修复，则在 master 上创建 bug 分支
  $ git checkout master
  $ git checkout -b issue-101
  
  # bug 分支上修复示例
  $ git add readme.txt
  $ git commit -m "fix bug 101"
  
  # 切换合并删除
  $ git checkout master
  $ git merge --no-ff -m "merged bug fix 101" issue-101
  
  # 返回干活
  $ git checkout dev
  $ git status
  # 工作区干净的？
  $ git stash list
  stash@{0}: WIP on dev: f52c633 add merge
  
  # way 1, 可以任意指定 list 中记录（list好多记录），但要手动删除原记录
  $ git stash apply stash@{0}
  $ git stash drop stash@{0}
  
  # way 2, 直接 pop ，不用 drop
  $ git stash pop
  
  # 之前修复的 bug，dev 上也要重复修复？ 不用
  # 可以直接将 bug 提交的修改 复制到 dev 分支
  $ git branch
  $ git cherry-pick <bug_commit_id>
  ```

  

- 特性分支

  Git 作为分布式版本管理系统，创建分支无需连接中央仓库，所以可以轻松地创建分支。大部分工作流程都用到特性（Topic）分支。

  特性分支，即集中实现单一特性（主题）。日常开发中，往往创建数个特性分支，同时再保留一个随时可以发布软件的稳定分支（一般由 master 分支担任）。

  基于特定主题的作业在特性分支中进行，主题完成后再与 master 分支合并。这样可以保证 master 分支可以随时供人查看，其他开发者也可以放心大胆地从 master 分支创建新的特性分支。

- 主干分支

  即特性分支的原点，也是合并的终点。有时需要让主干分支配置在正式环境中，有时又需要用标签 `Tag` 等创建版本信息，同时管理多个版本发布。拥有多个版本发布时，主干分支也有多个。

- 合并分支

  假设 feature-A 已实现完毕，现在合并至 master 中。

  ```shell
  # step 1
  $ git checkout master
  # step 2
  $ git merge --no-ff feature-A
  
  # 可以删除 feature-A 分支了
  $ git branch -d feature-A
  # 如果没有合并，无法 -d 删除分支，只能 -D
  $ git branch -D feature-A
  
  # 最后再查看下分支状态确认
  $ git branch
  
  # 可以用 switch 代替 branch
  $ git switch -c dev
  $ git switch master
  ```

  

- 图表形式查看分支

  ```shell
  # 图表形式查看提交日志，很直观
  $ git log --graph
  
  # 简洁版
  $ git log --graph --pretty=oneline --abbrev-commit
  ```

- 多人协作推送和抓取分支

  ```shell
  # 推送分支时，要指定本地分支，这样 Git 就会把该分支推送到远程库对应的远程分支上
  $ git push origin master
  
  # 如果推送其他分支，如 dev
  $ git push origin dev
  ```

  多人协作时，大家都会往 `master` 和 `dev` 分支推送各自修改。

  一般从远程库 clone 时，默认只能看到本地的 `master` 分支

  ```shell
  $ git clone git@github.com:test/test.git
  $ git branch
  * master
  ```

  如果要在 `dev` 分支上开发，必须创建远程 `origin` 的 `dev` 分支到本地

  ```shell
  $ git chechout -b dev origin/dev
  
  # add commit 后随时将 dev 分支 push 到远程
  $ git push origin dev
  ```

  如果同一个 `dev` 提交有冲突，根据 Git 提示，先用 `git pull` 将最新提交从 `origin/dev` 上抓取，然后本地合并，解决冲突后再提交

  ```shell
  $ git pull
  # 提示失败？ 没有制定本地 dev 分支 与远程 origin/dev 分支的链接
  $ git branch --set-upstream-to=origin/dev
  # try pull again
  $ git pull
  # pull 成功，但显示合并有冲突，手动解决后
  $ git commit -m "fix env conflict"
  # push 
  $ git push origin dev
  ```

- 多人协作的通用模式

  1. 首先试图 `git push origin <branch-name>` 推送自己的修改
  2. 如果推送失败，则因为远程分支比本地要新，需要先用 `git pull` 试图合并
  3. 如果合并有冲突，则解决冲突，并在本地提交
  4. 没有冲突或解决完冲突后，再用 `git push origin <branch-name>` 推送成功

  如果 `git pull` 提示 `no tracking information` ，则说明本地分支和远程分支的链接关系未建立，使用 `git branch --set-upstream-to <branch-name> origin/<branch-name>` 建立链接。

## 3.更改提交的操作

- 回溯历史版本

  提供目标时间点的哈希值，可以让仓库的 HEAD、暂存区、当前工作区回溯到指定状态。

  ```shell
  # 回溯到 feature-A 之前，并创建分支
  $ git reset --hard commit_id
  $ git checkout -b fix-B
  
  # 推进至 feature-A 分支合并后的状态
  # git log 只能查看以当前状态为终点的历史日志，reflog 可以查看当前仓库的操作日志
  $ git reflog
  # 找到 feature-A 特性分支合并后的状态
  $ git checkout master
  $ git reset --hard commit_id
  
  # 合并 fix-B
  $ git merge --no-ff fix-B
  
  # 回溯到上一个版本
  $ git reset --hard HEAD^
  
  # log 查不到 用 reflog 查所有的 操作，找到相应 hash 回溯
  
  # HEAD 指向的版本就是当前版本
  ```

  

- 消除冲突

  往往合并后不同分支有冲突，需要打开文件解决冲突（冲突的内容会并存在文件中，实际开发中往往需要删除之一），然后提交 `git add` & `git commit`。

- 修改提交信息

  通过 `git commit --amend` 修改提交信息

- 压缩历史

  简洁提交不必要的版本。如合并特性分支前，发现已提交的内容有拼写错误，可以提交一个修改并与前一个提交压缩成一个历史记录。

  ```shell
  $ git rebase -i
  ```

## 4. 标签管理

- 创建标签

  ```shell
  $ git branch
  $ git checkout master
  $ git tag v1.0
  
  # 查看所有 tag
  $ git tag
  # 查看标签信息
  $ git show v1.0
  # 创建带说明的标签
  $ git tag -a v0.1 -m "version 0.1 released" <commit_id>
  
  # 默认打在最新提交的 commit 上的，指定 commit_id 
  $ git log --pretty=oneline --abbrev-commit
  $ git tag v0.9 <commit_id>
  ```

  

- 操作标签

  ```shell
  # 创建的标签只存储在本地，不会自动推送到远程，本地删除很方便
  $ git tag -d v0.1
  
  # 推送某个 tag 远程？
  $ git push origin v1.0
  
  # 一次性推送全部未推送的 tag ？
  $ git push origin --tags
  
  # tag 推送远程后删除？
  # step 1, delete local
  $ git tag -d v0.9
  # step 2, delete remote
  $ git push origin:refs/tags/v0.9
  # step 3, login on github to check...
  ```

  