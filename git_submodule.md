[pro GIT](https://github.com/progit/progit2 )      [download pdf](https://github.com/progit/progit2/releases/download/2.1.304/progit.pdf) 

# Submodules

```简介
Submodules allow you to keep a Git repository as a subdirectory of another Git repository. 
This lets you clone another repository into your project and keep your commits separate.
Git子模块允许你将一个 Git 仓库作为另一个 Git 仓库的子目录。 它能让你将另一个仓库克隆到自己的项目中，同时还保持提交的独立。
```

## Starting with Submodules

我们首先将一个已存在的 Git 仓库添加为正在工作的仓库的子模块。 你可以通过在**<font color=#B12146>git submodule add  </font>**命令
后面加上想要跟踪的项目的相对或绝对 URL 来添加新的子模块。 在本例中，我们将会添加一个名为
“DbConnector” 的库。

```bash  
$ git submodule add https://github.com/chaconinc/DbConnector
Cloning into 'DbConnector'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
```



### .gitmodules 文件 

该配置文件保存了项目 URL 与已经拉取的本地目录之间的映射  

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)
  
	new file: .gitmodules
	new file: DbConnector
```

```bash
[submodule "DbConnector"]
	path = DbConnector
	url = https://github.com/chaconinc/DbConnector
```

如果有多个子模块，该文件中就会有多条记录。 要重点注意的是，该文件也像**<font color=#B12146> .gitignore </font>** 文件一样受到（通
过）版本控制。 它会和该项目的其他部分一同被拉取推送。 这就是克隆该项目的人知道去哪获得子模块的原
因。  

>由于 .gitmodules 文件中的 URL 是人们首先尝试克隆/拉取的地方，因此请尽可能确保你使用
>的 URL 大家都能访问。 例如，若你要使用的推送 URL 与他人的拉取 URL 不同，那么请使用
>他人能访问到的 URL。 你也可以根据自己的需要，通过在本地执行**<font color=#B12146>git config   </font>** 
>
>**<font color=#B12146>submodule.DbConnector.url <私有URL>      </font>** 来覆盖这个选项的值。 如果可行的话，一个相
>对路径会很有帮助。  



**<font color=#B12146>git diff  </font>** 传递 **<font color=#B12146>--submodule </font>**选项可看到漂亮的差异输出 。

```bash
$ git diff --cached --submodule
diff --git a/.gitmodules b/.gitmodules
new file mode 100644
index 0000000..71fc376
--- /dev/null
+++ b/.gitmodules
@@ -0,0 +1,3 @@
+[submodule "DbConnector"]
+ path = DbConnector
+ url = https://github.com/chaconinc/DbConnector
Submodule DbConnector 0000000...c3f01dc (new submodule)
```



当你提交时，会看到类似下面的信息：  

```bash
$ git commit -am 'added DbConnector module'
[master fb9093c] added DbConnector module
2 files changed, 4 insertions(+)
create mode 100644 .gitmodules
create mode 160000 DbConnector
```

注意**<font color=#B12146>DbConnector </font>**  记录的 **<font color=#B12146>160000</font>** 模式。 这是 Git 中的一种特殊模式，它本质上意味着你是将一次提交记作
一项目录记录的，而非将它记录成一个子目录或者一个文件。  

推送这些更改：

```bash
$ git push origin master  
```



### 克隆含有子模块的项目

接下来我们将会克隆一个含有子模块的项目。 当你在克隆这样的项目时，默认会包含该子模块目录，但其中还
没有任何文件：  

```bash
$ git clone https://github.com/chaconinc/MainProject
Cloning into 'MainProject'...
remote: Counting objects: 14, done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 14 (delta 1), reused 13 (delta 0)
Unpacking objects: 100% (14/14), done.
Checking connectivity... done.
$ cd MainProject
$ ls -la
total 16
drwxr-xr-x 9 schacon staff 306 Sep 17 15:21 .
drwxr-xr-x 7 schacon staff 238 Sep 17 15:21 ..
drwxr-xr-x 13 schacon staff 442 Sep 17 15:21 .git
-rw-r--r-- 1 schacon staff 92 Sep 17 15:21 .gitmodules
drwxr-xr-x 2 schacon staff 68 Sep 17 15:21 DbConnector
-rw-r--r-- 1 schacon staff 756 Sep 17 15:21 Makefile
drwxr-xr-x 3 schacon staff 102 Sep 17 15:21 includes
drwxr-xr-x 4 schacon staff 136 Sep 17 15:21 scripts
drwxr-xr-x 4 schacon staff 136 Sep 17 15:21 src
$ cd DbConnector/
$ ls
$
```

其中有**<font color=#B12146>DbConnector </font>** 目录，不过是空的。 你必须运行两个命令：**<font color=#B12146>git submodule init</font>**  用来初始化本地配
置文件，而**<font color=#B12146>git submodule update </font>**  则从该项目中抓取所有数据并检出父项目中列出的合适的提交。  

```bash
$ git submodule init
Submodule 'DbConnector' (https://github.com/chaconinc/DbConnector)
registered for path 'DbConnector'
$ git submodule update
Cloning into 'DbConnector'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
Submodule path 'DbConnector': checked out
'c3f01dc8862123d317dd46284b05b6892c7b29bc'
```



现在**<font color=#B12146>DbConnector </font>** 子目录是处在和之前提交时相同的状态了。
不过还有更简单一点的方式。 如果给 **<font color=#B12146>git clone </font>** 命令传递 **<font color=#B12146>--recurse-submodules </font>** 选项，它就会自动初始
化并更新仓库中的每一个子模块， 包括可能存在的嵌套子模块。  

```bash
$ git clone --recurse-submodules https://github.com/chaconinc/MainProject
Cloning into 'MainProject'...
remote: Counting objects: 14, done.
remote: Compressing objects: 100% (13/13), done.
remote: Total 14 (delta 1), reused 13 (delta 0)
Unpacking objects: 100% (14/14), done.
Checking connectivity... done.
Submodule 'DbConnector' (https://github.com/chaconinc/DbConnector)
registered for path 'DbConnector'
Cloning into 'DbConnector'...
remote: Counting objects: 11, done.
remote: Compressing objects: 100% (10/10), done.
remote: Total 11 (delta 0), reused 11 (delta 0)
Unpacking objects: 100% (11/11), done.
Checking connectivity... done.
Submodule path 'DbConnector': checked out
'c3f01dc8862123d317dd46284b05b6892c7b29bc
```



如果你已经克隆了项目但忘记了**<font color=#B12146>--recurse-submodules</font>**，那么可以运行 **<font color=#B12146>git submodule update
--init</font>** 将 **<font color=#B12146>git submodule init </font>** 和 **<font color=#B12146>git submodule update </font>** 合并成一步。如果还要初始化、抓取并检出
任何嵌套的子模块， 请使用简明的**<font color=#B12146>git submodule update --init --recursive </font>** 。  



### 在包含子模块的项目上工作  

#### 从子模块的远端拉取上游修改  

如果想要在子模块中查看新工作，可以进入到目录中运行**<font color=#B12146>git fetch</font>**  与 **<font color=#B12146>git merge</font>**，合并上游分支来更新本
地代码。  

```bash
$ git fetch
From https://github.com/chaconinc/DbConnector
	c3f01dc..d0354fc master -> origin/master
$ git merge origin/master
Updating c3f01dc..d0354fc
Fast-forward
  scripts/connect.sh | 1 +
  src/db.c | 1 +
  2 files changed, 2 insertions(+)
```



如果你现在返回到主项目并运行 **<font color=#B12146>git diff --submodule</font>**，就会看到子模块被更新的同时获得了一个包含新
添加提交的列表。 如果你不想每次运行  **<font color=#B12146>git diff </font>**时都输入  **<font color=#B12146>--submodle</font>**，那么可以将  **<font color=#B12146>diff.submodule </font>**设置为 “log” 来将其作为默认行为。  

```bash
$ git config --global diff.submodule log
$ git diff
Submodule DbConnector c3f01dc..d0354fc:
  > more efficient db routine
  > better connection routine
```

如果在此时提交，那么你会将子模块锁定为其他人更新时的新代码。
如果你不想在子目录中手动抓取与合并，那么还有种更容易的方式。 运行  **<font color=#B12146>git submodule update
--remote </font>**，Git 将会进入子模块然后抓取并更新。  

```bash
$ git submodule update --remote DbConnector
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 2), reused 4 (delta 2)
Unpacking objects: 100% (4/4), done.
From https://github.com/chaconinc/DbConnector
	3f19983..d0354fc master -> origin/master
Submodule path 'DbConnector': checked out
'd0354fc054692d3906c85c3af05ddce39a1c0644'
```

此命令默认会假定你想要更新并检出子模块仓库的**<font color=#B12146>master </font>** 分支。 不过你也可以设置为想要的其他分支。 例
如，你想要**<font color=#B12146>DbConnector </font>**子模块跟踪仓库的 “stable” 分支，那么既可以在**<font color=#B12146>.gitmodules  </font>** 文件中设置 （这
样其他人也可以跟踪它），也可以只在本地的 **<font color=#B12146>.git/config </font>** 文件中设置。 让我们在**<font color=#B12146>.gitmodules </font>** 文件中设
置它：  

```bash
$ git config -f .gitmodules submodule.DbConnector.branch stable

$ git submodule update --remote
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 2), reused 4 (delta 2)
Unpacking objects: 100% (4/4), done.
From https://github.com/chaconinc/DbConnector
	27cf5d3..c87d55d stable -> origin/stable
Submodule path 'DbConnector': checked out
'c87d55d4c6d4b05ee34fbc8cb6f7bf4585ae6687'
```

如果不用 -f **<font color=#B12146>.gitmodules  </font>** 选项，那么它只会为你做修改。但是在仓库中保留跟踪信息更有意义一些，因为其
他人也可以得到同样的效果。
这时我们运行 **<font color=#B12146>git status  </font>** ，Git 会显示子模块中有“新提交”。  

```bash
$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)

  modified: .gitmodules
  modified: DbConnector (new commits)
no changes added to commit (use "git add" and/or "git commit -a")
```

如果你设置了配置选项**<font color=#B12146>status.submodulesummary </font>**  ，Git 也会显示你的子模块的更改摘要：  

```bash
$ git config status.submodulesummary 1

$ git status
On branch master
Your branch is up-to-date with 'origin/master'.

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)

	modified: .gitmodules
	modified: DbConnector (new commits)
	
Submodules changed but not updated:

* DbConnector c3f01dc...c87d55d (4):
  > catch non-null terminated lines
```

这时如果运行**<font color=#B12146> git diff </font>**，可以看到我们修改了**<font color=#B12146>.gitmodules  </font>** 文件，同时还有几个已拉取的提交需要提交到我们
自己的子模块项目中。  

```bash
$ git diff
diff --git a/.gitmodules b/.gitmodules
index 6fc0b3d..fd1cc29 100644
--- a/.gitmodules
+++ b/.gitmodules
@@ -1,3 +1,4 @@
[submodule "DbConnector"]
		path = DbConnector
		url = https://github.com/chaconinc/DbConnector
+		branch = stable
Submodule DbConnector c3f01dc..c87d55d:
  > catch non-null terminated lines
  > more robust error handling
  > more efficient db routine
  > better connection routine
```

这非常有趣，因为我们可以直接看到将要提交到子模块中的提交日志。 提交之后，你也可以运行 **<font color=#B12146> git log -p</font>**
查看这个信息。  

```bash
$ git log -p --submodule
commit 0a24cfc121a8a3c118e0105ae4ae4c00281cf7ae
Author: Scott Chacon <schacon@gmail.com>
Date: Wed Sep 17 16:37:02 2014 +0200

	updating DbConnector for bug fixes

diff --git a/.gitmodules b/.gitmodules
index 6fc0b3d..fd1cc29 100644
--- a/.gitmodules
+++ b/.gitmodules
@@ -1,3 +1,4 @@
 [submodule "DbConnector"]
		path = DbConnector
		url = https://github.com/chaconinc/DbConnector
+ 		branch = stable
Submodule DbConnector c3f01dc..c87d55d:
  > catch non-null terminated lines
  > more robust error handling
  > more efficient db routine
  > better connection routine
```

当运行**<font color=#B12146>git submodule update --remote </font>** 时，Git 默认会尝试更新 **所有** 子模块， 所以如果有很多子模块的
话，你可以传递想要更新的子模块的名字。  



#### 从项目远端拉取上游更改  

现在，让我们站在协作者的视角，他有自己的 **<font color=#B12146>MainProject</font>** 仓库的本地克隆， 只是执行 **<font color=#B12146>git pull</font>** 获取你新
提交的更改还不够：  

```bash
$ git pull
From https://github.com/chaconinc/MainProject
	fb9093c..0a24cfc master -> origin/master
Fetching submodule DbConnector
From https://github.com/chaconinc/DbConnector
	c3f01dc..c87d55d stable -> origin/stable
Updating fb9093c..0a24cfc
Fast-forward
 .gitmodules | 2 +-
 DbConnector | 2 +-
 2 files changed, 2 insertions(+), 2 deletions(-)
 
$ git status
 On branch master
Your branch is up-to-date with 'origin/master'.
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working
directory)

	modified: DbConnector (new commits)

Submodules changed but not updated:
* DbConnector c87d55d...c3f01dc (4):
  < catch non-null terminated lines
  < more robust error handling
  < more efficient db routine
  < better connection routine
  
no changes added to commit (use "git add" and/or "git commit -a")
```

默认情况下， **<font color=#B12146>git pull</font>**命令会递归地抓取子模块的更改，如上面第一个命令的输出所示。 然而，它不会 **更新**子模块。这点可通过 **<font color=#B12146>git status</font>**  命令看到，它会显示子模块“已修改”，且“有新的提交”。 此外，左边的尖括号（<）指出了新的提交，表示这些提交已在**<font color=#B12146>MainProject</font>** 中记录，但尚未在本地的 **<font color=#B12146>DbConnector</font>** 中检出。 为了完成更新，你需要运行 **<font color=#B12146>git submodule update</font>**：  

```bash
$ git submodule update --init --recursive
Submodule path 'vendor/plugins/demo': checked out
'48679c6302815f6c76f1fe30625d795d9e55fc56'

$ git status
 On branch master
Your branch is up-to-date with 'origin/master'.
nothing to commit, working tree clean
```



请注意，为安全起见，如果 **<font color=#B12146>MainProject</font>**  提交了你刚拉取的新子模块，那么应该在  **<font color=#B12146>git submodule update</font>**
后面添加  **<font color=#B12146>--init</font>** 选项，如果子模块有嵌套的子模块，则应使用 **<font color=#B12146>--recursive</font>**  选项。
如果你想自动化此过程，那么可以为 **<font color=#B12146>git pull</font>** 命令添加  **<font color=#B12146>--recurse-submodules</font>**  选项（从 Git 2.14 开
始）。 这会让 Git 在拉取后运行 **<font color=#B12146>git submodule update</font>**，将子模块置为正确的状态。 此外，如果你想让 Git
总是以**<font color=#B12146>--recurse-submodules</font>**  拉取，可以将配置选项**<font color=#B12146> submodule.recurse</font>**   设置为 **<font color=#B12146>true</font>** （从 Git 2.15 开
始可用于 **<font color=#B12146>git pull</font>**）。此选项会让 Git 为所有支持**<font color=#B12146>--recurse-submodules</font>**  的命令使用该选项（除**<font color=#B12146>clone</font>** 以外）。  



在为父级项目拉取更新时，还会出现一种特殊的情况：在你拉取的提交中， 可能**<font color=#B12146>.gitmodules  </font>** 文件中记录的
子模块的 URL 发生了改变。 比如，若子模块项目改变了它的托管平台，就会发生这种情况。 此时，若父级项目
引用的子模块提交不在仓库中本地配置的子模块远端上，那么执行 **<font color=#B12146> git pull --recurse-submodules</font>**  或
**<font color=#B12146>git submodule update</font>**就会失败。 为了补救，**<font color=#B12146>git submodule sync</font>** 命令需要：  

```bash
# 将新的 URL 复制到本地配置中
$ git submodule sync --recursive
# 从新 URL 更新子模块
$ git submodule update --init --recursive
```



#### 在子模块上工作  

你很有可能正在使用子模块，因为你确实想在子模块中编写代码的同时，还想在主项目上编写代码（或者跨子模
块工作）。 否则你大概只能用简单的依赖管理系统（如 Maven 或 Rubygems）来替代了。
现在我们将通过一个例子来演示如何在子模块与主项目中同时做修改，以及如何同时提交与发布那些修改。
到目前为止，当我们运行  **<font color=#B12146>git submodule update</font>**从子模块仓库中抓取修改时， Git 将会获得这些改动并更
新子目录中的文件，但是会将子仓库留在一个称作“游离的 HEAD”的状态。 这意味着没有本地工作分支（例如
“master” ）跟踪改动。 如果没有工作分支跟踪更改，也就意味着即便你将更改提交到了子模块，这些更改也
很可能会在下次运行  **<font color=#B12146>git submodule update</font>** 时丢失。如果你想要在子模块中跟踪这些修改，还需要一些额
外的步骤。
为了将子模块设置得更容易进入并修改，你需要做两件事。 首先，进入每个子模块并检出其相应的工作分支。
接着，若你做了更改就需要告诉 Git 它该做什么，然后运行**<font color=#B12146>git submodule update --remote </font>** 来从上游拉
取新工作。 你可以选择将它们合并到你的本地工作中，也可以尝试将你的工作变基到新的更改上。

首先，让我们进入子模块目录然后检出一个分支。  

```bash
$ cd DbConnector/
$ git checkout stable
Switched to branch 'stable'
```

然后尝试用 “merge” 选项来更新子模块。 为了手动指定它，我们只需给 **<font color=#B12146> update </font>**添加 **<font color=#B12146> --merge </font>**选项即可。
这时我们将会看到服务器上的这个子模块有一个改动并且它被合并了进来。  

```bash
$ cd ..
$ git submodule update --remote --merge
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 4 (delta 2), reused 4 (delta 2)
Unpacking objects: 100% (4/4), done.
From https://github.com/chaconinc/DbConnector
	c87d55d..92c7337 stable -> origin/stable
Updating c87d55d..92c7337
Fast-forward
  src/main.c | 1 +
  1 file changed, 1 insertion(+)
Submodule path 'DbConnector': merged in
'92c7337b30ef9e0893e758dac2459d07362ab5ea'
```

如果我们进入**<font color=#B12146> DbConnector  </font>**目录，可以发现新的改动已经合并入本地 **<font color=#B12146> stable</font>** 分支。 现在让我们看看当我们对
库做一些本地的改动而同时其他人推送另外一个修改到上游时会发生什么。  

```bash
$ cd DbConnector/
$ vim src/db.c
$ git commit -am 'unicode support'
[stable f906e16] unicode support
 1 file changed, 1 insertion(+)
```

如果我们现在更新子模块，就会看到当我们在本地做了更改时上游也有一个改动，我们需要将它并入本地。  

```bash
$ cd ..
$ git submodule update --remote --rebase
First, rewinding head to replay your work on top of it...
Applying: unicode support
Submodule path 'DbConnector': rebased into
'5d60ef9bbebf5a0c1c1050f242ceeb54ad58da94'
```

如果你忘记  **<font color=#B12146> --rebase</font>** 或  **<font color=#B12146> --merge </font>**，Git 会将子模块更新为服务器上的状态。并且会将项目重置为一个游离的
HEAD 状态。  

```bash
$ git submodule update --remote
Submodule path 'DbConnector': checked out
'5d60ef9bbebf5a0c1c1050f242ceeb54ad58da94'
```

即便这真的发生了也不要紧，你只需回到目录中再次检出你的分支（即还包含着你的工作的分支）然后手动地合
并或变基 **<font color=#B12146>origin/stable</font>** （或任何一个你想要的远程分支）就行了。
如果你没有提交子模块的改动，那么运行一个子模块更新也不会出现问题，此时 Git 会只抓取更改而并不会覆盖
子模块目录中未保存的工作。  

```bash
$ git submodule update --remote
remote: Counting objects: 4, done.
remote: Compressing objects: 100% (3/3), done.
remote: Total 4 (delta 0), reused 4 (delta 0)
Unpacking objects: 100% (4/4), done.
From https://github.com/chaconinc/DbConnector
	5d60ef9..c75e92a stable -> origin/stable
error: Your local changes to the following files would be overwritten by
checkout:
	 scripts/setup.sh
Please, commit your changes or stash them before you can switch branches.
Aborting
Unable to checkout 'c75e92a2b3855c9e5b66f915308390d9db204aca' in submodule
path 'DbConnector
```

如果你做了一些与上游改动冲突的改动，当运行更新时 Git 会让你知道。  

```bash
$ git submodule update --remote --merge
Auto-merging scripts/setup.sh
CONFLICT (content): Merge conflict in scripts/setup.sh
Recorded preimage for 'scripts/setup.sh'
Automatic merge failed; fix conflicts and then commit the result.
Unable to merge 'c75e92a2b3855c9e5b66f915308390d9db204aca' in submodule
path 'DbConnector'
```

你可以进入子模块目录中然后就像平时那样修复冲突。  



#### 发布子模块改动

现在我们的子模块目录中有一些改动。 其中有一些是我们通过更新从上游引入的，而另一些是本地生成的，由
于我们还没有推送它们，所以对任何其他人都不可用。  

```bash
$ git diff
Submodule DbConnector c87d55d..82d2ad3:
  > Merge from origin/stable
  > updated setup script
  > unicode support
  > remove unnecessary method
  > add new option for conn pooling
```

如果我们在主项目中提交并推送但并不推送子模块上的改动，其他尝试检出我们修改的人会遇到麻烦， 因为他
们无法得到依赖的子模块改动。那些改动只存在于我们本地的拷贝中。
为了确保这不会发生，你可以让 Git 在推送到主项目前检查所有子模块是否已推送。 **<font color=#B12146>git push</font>** 命令接受可以设
置为 “check” 或 “on-demand” 的 **<font color=#B12146>--recurse-submodules</font>**  参数。 如果任何提交的子模块改动没有推送
那么 “check” 选项会直接使  **<font color=#B12146>push</font>**  操作失败。  

```bash
$ git push --recurse-submodules=check
The following submodule paths contain changes that can
not be found on any remote:
	DbConnector
	
Please try

	git push --recurse-submodules=on-demand
	
or cd to the path and use

	git push
	
to push them to a remote.
```

如你所见，它也给我们了一些有用的建议，指导接下来该如何做。 最简单的选项是进入每一个子模块中然后手
动推送到远程仓库，确保它们能被外部访问到，之后再次尝试这次推送。 如果你想要对所有推送都执行检查，
那么可以通过设置**<font color=#B12146>git config push.recurseSubmodules check</font>** 让它成为默认行为。

另一个选项是使用 “on-demand” 值，它会尝试为你这样做。  

```bash
$ git push --recurse-submodules=on-demand
Pushing submodule 'DbConnector'
Counting objects: 9, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (8/8), done.
Writing objects: 100% (9/9), 917 bytes | 0 bytes/s, done.
Total 9 (delta 3), reused 0 (delta 0)
To https://github.com/chaconinc/DbConnector
	c75e92a..82d2ad3 stable -> stable
Counting objects: 2, done.
Delta compression using up to 8 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (2/2), 266 bytes | 0 bytes/s, done.
Total 2 (delta 1), reused 0 (delta 0)
To https://github.com/chaconinc/MainProject
	3d6d338..9a377d1 master -> master
```

如你所见，Git 进入到 DbConnector 模块中然后在推送主项目前推送了它。 如果那个子模块因为某些原因推送
失败，主项目也会推送失败。 你也可以通过设置**<font color=#B12146>git config push.recurseSubmodules on-demand</font>**让
它成为默认行为。  



#### 合并子模块改动  

如果你与其他人同时改动了一个子模块引用，那么可能会遇到一些问题。 也就是说，如果子模块的历史已经分叉
并且在父项目中分别提交到了分叉的分支上，那么你需要做一些工作来修复它。
如果一个提交是另一个的直接祖先（一个快进式合并），那么 Git 会简单地选择之后的提交来合并，这样没什么
问题。
不过，Git 甚至不会尝试去进行一次简单的合并。 如果子模块提交已经分叉且需要合并，那你会得到类似下面的
信息：  

```bash
$ git pull
remote: Counting objects: 2, done.
remote: Compressing objects: 100% (1/1), done.
remote: Total 2 (delta 1), reused 2 (delta 1)
Unpacking objects: 100% (2/2), done.
From https://github.com/chaconinc/MainProject
	9a377d1..eb974f8 master -> origin/master
Fetching submodule DbConnector
warning: Failed to merge submodule DbConnector (merge following commits not found)
Auto-merging DbConnector
CONFLICT (submodule): Merge conflict in DbConnector
Automatic merge failed; fix conflicts and then commit the result.
```

所以本质上 Git 在这里指出了子模块历史中的两个分支记录点已经分叉并且需要合并。 它将其解释为 “merge
following commits not found” （未找到接下来需要合并的提交），虽然这有点令人困惑，不过之后我们会解
释为什么是这样。

为了解决这个问题，你需要弄清楚子模块应该处于哪种状态。 奇怪的是，Git 并不会给你多少能帮你摆脱困境的
信息，甚至连两边提交历史中的 SHA-1 值都没有。 幸运的是，这很容易解决。 如果你运行 **<font color=#B12146>git diff</font>**，就会得
到试图合并的两个分支中记录的提交的 SHA-1 值。  

```bash
$ git diff
diff --cc DbConnector
index eb41d76,c771610..0000000
--- a/DbConnector
+++ b/DbConnector
```

所以，在本例中， **<font color=#B12146>eb41d76</font>** 是我们的子模块中**大家共有**的提交，而  **<font color=#B12146>c771610</font>** 是上游拥有的提交。 如果我们进
入子模块目录中，它应该已经在  **<font color=#B12146>eb41d76</font>** 上了，因为合并没有动过它。 如果不是的话，无论什么原因，你都可
以简单地创建并检出一个指向它的分支。

来自另一边的提交的 SHA-1 值比较重要。 它是需要你来合并解决的。 你可以尝试直接通过 SHA-1 合并，也可以
为它创建一个分支然后尝试合并。 我们建议后者，哪怕只是为了一个更漂亮的合并提交信息。

所以，我们将会进入子模块目录，基于 **<font color=#B12146>git diff</font>** 的第二个 SHA-1 创建一个分支然后手动合并。  

```bash
$ cd DbConnector

$ git rev-parse HEAD
eb41d764bccf88be77aced643c13a7fa86714135

$ git branch try-merge c771610
(DbConnector) $ git merge try-merge
Auto-merging src/main.c
CONFLICT (content): Merge conflict in src/main.c
Recorded preimage for 'src/main.c'
Automatic merge failed; fix conflicts and then commit the result.
```

我们在这儿得到了一个真正的合并冲突，所以如果想要解决并提交它，那么只需简单地通过结果来更新主项目。  

```bash
$ vim src/main.c ①
$ git add src/main.c
$ git commit -am 'merged our changes'
Recorded resolution for 'src/main.c'.
[master 9fd905e] merged our changes

$ cd .. ②
$ git diff ③
diff --cc DbConnector
index eb41d76,c771610..0000000
--- a/DbConnector
+++ b/DbConnector
@@@ -1,1 -1,1 +1,1 @@@
- Subproject commit eb41d764bccf88be77aced643c13a7fa86714135
-Subproject commit c77161012afbbe1f58b5053316ead08f4b7e6d1d
++Subproject commit 9fd905e5d7f45a0d4cbc43d1ee550f16a30e825a
$ git add DbConnector ④

$ git commit -m "Merge Tom's Changes" ⑤
[master 10d2c60] Merge Tom's Changes
```



① 首先解决冲突
② 然后返回到主项目目录中
③ 再次检查 SHA-1 值
④ 解决冲突的子模块记录
⑤ 提交我们的合并
这可能会让你有点儿困惑，但它确实不难。
有趣的是，Git 还能处理另一种情况。 如果子模块目录中存在着这样一个合并提交，它的历史中包含了的**两边**的
提交，那么 Git 会建议你将它作为一个可行的解决方案。 它看到有人在子模块项目的某一点上合并了包含这两次
提交的分支，所以你可能想要那个。
这就是为什么前面的错误信息是 “merge following commits not found”，因为它不能 **这样** 做。 它让人困惑
是因为**谁能想到它会尝试这样做？
**如果它找到了一个可以接受的合并提交，你会看到类似下面的信息：  

```bash
$ git merge origin/master
warning: Failed to merge submodule DbConnector (not fast-forward)
Found a possible merge resolution for the submodule:
 9fd905e5d7f45a0d4cbc43d1ee550f16a30e825a: > merged our changes
If this is correct simply add it to the index for example
by using:

  git update-index --cacheinfo 160000
9fd905e5d7f45a0d4cbc43d1ee550f16a30e825a "DbConnector"

which will accept this suggestion.
Auto-merging DbConnector
CONFLICT (submodule): Merge conflict in DbConnector
Automatic merge failed; fix conflicts and then commit the result.
```

Git 建议的命令是更新索引，就像你运行了**<font color=#B12146>git add</font>** 那样，这样会清除冲突然后提交。 不过你可能不应该这样
做。你可以轻松地进入子模块目录，查看差异是什么，快进到这次提交，恰当地测试，然后提交它。  

```bash
$ cd DbConnector/
$ git merge 9fd905e
Updating eb41d76..9fd905e
Fast-forward

$ cd ..
$ git add DbConnector
$ git commit -am 'Fast forwarded to a common submodule child'
```

这些命令完成了同一件事，但是通过这种方式你至少可以验证工作是否有效，以及当你在完成时可以确保子模块
目录中有你的代码。  
