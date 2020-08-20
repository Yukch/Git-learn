
笔记整理来源：[廖雪峰Git教程](https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000)

### 基本操作
- ` git init` 初始化一个Git仓库。
- 添加文件到Git仓库，分两步：
   `git add <file>`，注意，可反复多次使用，添加多个文件；
   `git commit -m "message"` 对添加的文件进行提交。
- `git status` 查看工作区的状态。
- `git diff <file>`如果文件被修改，可以查看修改内容。
- `git reset --hard commit_id`HEAD指向的版本就是当前版本，HEAD^指向前一个版本，因此，Git允许我们在版本的历史之间穿梭。
- `git log [pretty=oneline] [--abbrev-commit]`可以查看提交历史，以便确定要回退到哪个版本。
- `git reflog`查看命令历史，以便确定要回到未来的哪个版本
- `git rm <file>`用于删除一个文件。如果一个文件已经被提交到版本库，那么你永远不用担心误删，但是要小心，你只能恢复文件到最新版本，你会丢失最近一次提交后你修改的内容。

### 错误修改
>Git跟踪并管理的是**修改**，而非**文件**。

- **场景1：**`git checkout -- file`当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时。

- **场景2：**`git reset HEAD <file>`当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步：第一步用命令，就回到了场景1，第二步按场景1操作。

- **场景3：** 已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节（使用`git reset`），不过前提是没有推送到远程库。

### 远程仓库

- 创建SSH Key。`$ ssh-keygen -t rsa -C "youremail@example.com"`，可以在用户主目录里找到`.ssh`目录，里面有`id_rsa`和`id_rsa.pub`两个文件
- 登陆GitHub，打开**“Account settings”**，**“SSH Keys”**页面：
- 然后，点**“Add SSH Key”**，填上任意Title，在Key文本框里粘贴`id_rsa.pub`文件的内容：
- 要关联一个远程库，使用命令`git remote add origin git@github.com:michaelliao/learngit.git`
- 关联后，使用命令`git push -u origin master`第一次推送master分支的所有内容；
- 此后，每次本地提交后，只要有必要，就可以使用命令`git push origin master`推送最新修改；
- 要克隆一个仓库，首先必须知道仓库的地址，然后使用`git clone <地址>`命令克隆。
- Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。


### 分支管理
>Git鼓励大量使用分支

- `git branch` 查看分支：
- `git branch <name>`创建分支：
- `git checkout <name>`或`git switch <name>`切换分支：
- `git checkout -b <name>`或``git switch -c <name>``创建+切换分支：
- `git merge <name>`合并某分支到当前分支：
- `git branch -d <name>`删除分支：


### 冲突管理

>当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。

- 解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交，就是自己打开文件编辑。
用`git log --graph`命令可以看到分支合并图
- Git分支十分强大，在团队开发中应该充分应用。
- 合并分支时，加上`--no-ff`参数就可以用普通模式合并，合并后的历史有分支，能看出来曾经做过合并，而`fast forward`合并就看不出来曾经做过合并，会丢掉分支信息。

### bug修复
- 修复bug时，我们会通过创建新的bug分支进行修复，然后合并，最后删除；
- 修复前，如果手头工作没有完成时，先把工作现场`git stash`一下，然后去修复bug。
- 修复后。可以通过`git stash list`查看`stash`列表，用`git stash apply stash @{0}`恢复。
-  再`git stash pop`删除`stash`，回到工作现场。

### 标签管理
>标签也是版本库的一个快照，其实它就是指向某个commit的指针，tag就是容易记住的有意义的名字，跟某个commit绑在一起。
- `git tag v1.0`切换到某个分支后可以采用命令进行打标签。
- `git tag v0.9 commit-id`默认标签是打在最新提交的commit上的。有时需要对过去某次commit打标签。
- `git tag`查看所有标签。
- `git show <tagname>`查看标签信息。
- `git tag -a <tagname> -m "blablabla..."`创建带有说明的标签，用-a指定标签名，-m指定说明文字。
- `git tag -d v0.1`删除标签。
- `git push origin <tagname>`推送某个标签。
- `git push origin --tags`一次性推送全部尚未推送到远程的本地标签。
- `git push origin :refs/tags/<tagname>`若标签已经推送到远程，要进行删除，先删除本地，再使用命令删除远程标签。

### 多人协作
- 软件开发中，总有无穷无尽的新的功能要不断添加进来。
- 添加一个新功能时，你肯定不希望因为一些实验性质的代码，把主分支搞乱了。
- 所以，每添加一个新功能，最好新建一个feature分支，在上面开发，完成后，合并，最后，删除该feature分支。
- 如果要丢弃一个没有被合并过的分支，可以通过`git branch -D <name>`强行删除。
- 因此，**多人协作的工作模式**通常是这样：
	- 首先，可以试图用`git push origin <branch-name>`推送自己的修改；
	- 如果推送失败，则因为远程分支比你的本地更新，需要先用`git pull`试图合并；
	- 如果合并有冲突，则解决冲突，并在本地提交；
	- 没有冲突或者解决掉冲突后，再用`git push origin <branch-name>`推送就能成功！
	- 如果`git pull`提示`no tracking information`，则说明本地分支和远程分支的链接关系没有创建，用命令`git branch --set-upstream-to <branch-name> origin/<branch-name>`。
- 这就是多人协作的工作模式，一旦熟悉了，就非常简单。

- 查看远程库信息，使用`git remote -v`；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用`git push origin branch-name`，如果推送失败，先用`git pull`抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用`git checkout -b branch-name origin/branch-name`，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用`git branch --set-upstream branch-name origin/branch-name`；
- 从远程抓取分支，使用`git pull`，如果有冲突，要先处理冲突。

### 其他
- 在GitHub上，可以任意Fork开源仓库；自己拥有**Fork**后的仓库的读写权限；
- 可以推送**pull request**给官方仓库来贡献代码。
- 忽略某些文件时，需要编写`.gitignore`；` .gitignore`文件本身要放到版本库里，并且可以对`.gitignore`做版本管理！
