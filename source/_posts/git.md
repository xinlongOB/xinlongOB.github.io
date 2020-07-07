---
title: git基础操作
tags:
  - git
  linux
categories: 运维
date: 2020-06-09 10:29:00
---
## Git安装
yum安装
```bash
yum install git -y
```
源码安装
```bash
# 下载源码包
cd /opt/
sudo wget https://github.com/git/git/archive/v2.14.1.zip
unzip v2.14.1.zip
# 安装依赖包
 sudo  yum  install curl-devel expat-devel gettext-devel openssl-devel zlib-devel gcc perl-ExtUtils-MakeMaker   -y 
# 进入git-2.3.0，
cd git-2.14.1
#配置参数
sudo make prefix=/usr/local/ all
sudo make prefix=/usr/local/ install
# 等待一切安装完成，用git --version查看版本号，能看到即可
git  --version
```
## Git工作流程
一般工作流程如下：

    克隆 Git 资源作为工作目录。
    在克隆的资源上添加或修改文件。
    如果其他人修改了，你可以更新资源。
    在提交前查看修改。
    提交修改。
    在修改完成后，如果发现错误，可以撤回提交并再次修改并提交。

当执行提交操作（git commit）时，暂存区的目录树写到版本库（对象库）中，master 分支会做相应的更新。即 master 指向的目录树就是提交时暂存区的目录树。

当执行 "git reset HEAD" 命令时，暂存区的目录树会被重写，被 master 分支指向的目录树所替换，但是工作区不受影响。

当执行 "git rm --cached <file>" 命令时，会直接从暂存区删除文件，工作区则不做出改变。

当执行 "git checkout ." 或者 "git checkout -- <file>" 命令时，会用暂存区全部或指定的文件替换工作区的文件。这个操作很危险，会清除工作区中未添加到暂存区的改动。

当执行 "git checkout HEAD ." 或者 "git checkout HEAD <file>" 命令时，会用 HEAD 指向的 master 分支中的全部或者部分文件替换暂存区和以及工作区中的文件。这个命令也是极具危险性的，因为不但会清除工作区中未提交的改动，也会清除暂存区中未提交的改动。 

## Git创建仓库
git init

Git 使用 git init 命令来初始化一个 Git 仓库，Git 的很多命令都需要在 Git 的仓库中运行，所以 git init 是使用 Git 的第一个命令。
在执行完成 git init 命令后，Git 仓库会生成一个 .git 目录，该目录包含了资源的所有元数据，其他的项目目录保持不变（不像 SVN 会在每个子目录生成 .svn 目录，Git 只在仓库的根目录生成 .git 目录）。

使用当前目录作为Git仓库，我们只需使它初始化。
```bash
git init
```
如果当前目录下有几个文件想要纳入版本控制，需要先用 git add 命令告诉 Git 开始对这些文件进行跟踪，然后提交： 
```bash
$ git add *.c
$ git add README
$ git commit -m '初始化项目版本'
```
以上命令将目录下以 .c 结尾及 README 文件提交到仓库中。

git clone
<br/>我们使用 git clone 从现有 Git 仓库中拷贝项目（类似 svn checkout）<br/>
```bash
git clone <repo>
```
如果我们需要克隆到指定的目录，可以使用以下命令格式：
```bash
git clone <repo> <directory>
```
参数说明：

    repo:Git 仓库。
    directory:本地目录

执行该命令后，会在当前目录下创建一个名为grit的目录，其中包含一个 .git 的目录，用于保存下载下来的所有版本记录。

如果要自己定义要新建的项目目录名称，可以在上面的命令末尾指定新的名字：
```bash
 git clone git://github.com/schacon/grit.git mygrit
 ```

 ## Git基本操作
创建仓库
```bash
[sgsm@centos git]$ mkdir git
[sgsm@centos git]$ cd  git
[sgsm@centos git]$ git  init
Initialized empty Git repository in /root/git/.git/
```
现在你可以看到在你的项目中生成了 .git 这个子目录。 这就是你的 Git 仓库了，所有有关你的此项目的快照数据都存放在这里
```bash
[sgsm@centos git]$ ll -a
total 12
drwxr-xr-x   3 sgsm sgsm 4096 Jun 12 13:50 .
dr-xr-x---. 11 sgsm sgsm 4096 Jun 12 13:31 ..
drwxr-xr-x   7 sgsm sgsm 4096 Jun 12 13:50 .git
```

克隆仓库
```bash
git  clone  https://github.com/xinlongOB/python.github.io.git
```

git add 
```
# 创建文件
touch  test.py
# 添加暂存区    . 代表当前目录   也可以指定文件名
git add  .
# 提交并添加注释
git commit -m " add  test.py"
```
报错：

    *** Please tell me who you are.

    Run

      git config --global user.email "you@example.com"
      git config --global user.name "Your Name"

    to set your account's default identity.
    Omit --global to set the identity only in this repository.

    fatal: unable to auto-detect email address (got 'root@suyuerunCentos.(none)')

因为没有添加账号密码
```bash
# 添加邮箱和账号
git config --global user.email "942868591@qq.com"
git config --global user.name "long"
```
再次提交
```bash
[sgsm@centos python.github.io]$ git commit -m " add  test.py"                    
[master 49151ac]  add  test.py
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 test.py
 ```
使用 git log 查看提交记录
```bash
[root@centos python.github.io]$ git log
commit 49151ac5859a2227e660c25cc5d3df07b2964974 (HEAD -> master)
Author: long <942868591@qq.com>
Date:   Fri Jun 12 14:05:48 2020 +0800

     add  test.py

```
git status
<br/>git status 以查看在你上次提交之后是否有修改<br/>

```bash
[root@centos python.github.io]$ touch import.py   # 创建文件
[root@centos python.github.io]$ git status  -s   # 查看是否有修改
?? import.py          # ? 代表不明状态  就是没有添加到暂存区
[root@centos python.github.io]$ git add .     # 提交
[root@centos python.github.io]$ git status  -s    # 查看是否有修改
A  import.py          # A  表示add   新增
```

git commit

使用 git add 命令将想要快照的内容写入缓存区， 而执行 git commit 将缓存区内容添加到仓库中。
<br/>Git 为你的每一个提交都记录你的名字与电子邮箱地址，所以第一步需要配置用户名和邮箱地址。<br/>

```bash
# 添加邮箱和账号
git config --global user.email "942868591@qq.com"
git config --global user.name "long"
```

git reset HEAD

git reset HEAD 命令用于取消已缓存的内容。
```bash
[sgsm@centos python.github.io]$ echo  "print("test")" > test.py 
[sgsm@centos python.github.io]$ echo  "print("import")" > import.py            
[sgsm@centos python.github.io]$ git add .
[sgsm@centos python.github.io]$ git status -s                        
A  import.py
M  test.py
[sgsm@centos python.github.io]$ git reset HEAD   test.py 
Unstaged changes after reset:
M       test.py
[sgsm@centos python.github.io]$ git status -s            
A  import.py
 M test.py
[sgsm@centos python.github.io]$ git config --global user.email "942868591@qq.com"
[sgsm@centos python.github.io]$ git config --global user.name "long"
[sgsm@centos python.github.io]$ git commit -m "update"                           
[master cbd780e] update
 1 file changed, 1 insertion(+)
 create mode 100644 import.py
# 提交日志显示只提交了一个文件 说明撤销成功了
```

git rm

如果只是简单地从工作目录中手工删除文件，运行 git status 时就会在 Changes not staged for commit 的提示。
```bash
# 要从 Git 中移除某个文件，就必须要从已跟踪文件清单中移除，然后提交。可以用以下命令完成此项工作
git rm <file> 
# 如果删除之前修改过并且已经放到暂存区域的话，则必须要用强制删除选项 -f
git rm -f <file>
# 如果把文件从暂存区域移除，但仍然希望保留在当前工作目录中，换句话说，仅是从跟踪清单中删除，使用 --cached 选项即可
git rm --cached <file>
#  例如删除已提交的文件：
[sgsm@centos python.github.io]$ git rm  -f test.py  
rm 'test.py'
[sgsm@centos python.github.io]$ ll
total 24
-rw-r--r-- 1 sgsm sgsm   14 Jun 12 14:23 import.py
-rw-r--r-- 1 sgsm sgsm 3744 Jun 12 13:53 plane_main.py
-rw-r--r-- 1 sgsm sgsm 5113 Jun 12 13:53 plane_sprites.py
-rw-r--r-- 1 sgsm sgsm  188 Jun 12 13:53 README.md
drwxr-xr-x 2 sgsm sgsm 4096 Jun 12 13:53 图片
[sgsm@centos python.github.io]$ 
```
```bash
# 例如从暂存区移除，但保留在当前工作目录
[sgsm@centos python.github.io]$ echo  "test"  > test.py 
[sgsm@centos python.github.io]$ git add  .    
[sgsm@centos python.github.io]$ git status -s 
M  test.py
[sgsm@centos python.github.io]$ ll
total 28
-rw-r--r-- 1 sgsm sgsm    14 Jun 12 14:23 import.py
-rw-r--r-- 1 sgsm sgsm  3744 Jun 12 13:53 plane_main.py
-rw-r--r-- 1 sgsm sgsm  5113 Jun 12 13:53 plane_sprites.py
-rw-r--r-- 1 sgsm sgsm   188 Jun 12 13:53 README.md
-rw-r--r-- 1 sgsm users    5 Jun 12 14:36 test.py
drwxr-xr-x 2 sgsm sgsm  4096 Jun 12 13:53 图片
[sgsm@centos python.github.io]$ git rm --cached test.py 
rm 'test.py'
[sgsm@centos python.github.io]$ ll
total 28
-rw-r--r-- 1 sgsm sgsm    14 Jun 12 14:23 import.py
-rw-r--r-- 1 sgsm sgsm  3744 Jun 12 13:53 plane_main.py
-rw-r--r-- 1 sgsm sgsm  5113 Jun 12 13:53 plane_sprites.py
-rw-r--r-- 1 sgsm sgsm   188 Jun 12 13:53 README.md
-rw-r--r-- 1 sgsm users    5 Jun 12 14:36 test.py
drwxr-xr-x 2 sgsm sgsm  4096 Jun 12 13:53 图片
[sgsm@centos python.github.io]$ git status -s 
D  test.py
?? test.py
[sgsm@centos python.github.io]$ 
```

 git mv

git mv 命令用于移动或重命名一个文件、目录、软连接
```bash
[sgsm@centos python.github.io]$ ll
total 24
-rw-r--r-- 1 sgsm sgsm   14 Jun 12 14:23 import.py
-rw-r--r-- 1 sgsm sgsm 3744 Jun 12 13:53 plane_main.py
-rw-r--r-- 1 sgsm sgsm 5113 Jun 12 13:53 plane_sprites.py
-rw-r--r-- 1 sgsm sgsm  188 Jun 12 13:53 README.md
drwxr-xr-x 2 sgsm sgsm 4096 Jun 12 13:53 图片
[sgsm@centos python.github.io]$ git status -s
[sgsm@centos python.github.io]$ git mv  import.py   print.py
[sgsm@centos python.github.io]$ ll
total 24
-rw-r--r-- 1 sgsm sgsm 3744 Jun 12 13:53 plane_main.py
-rw-r--r-- 1 sgsm sgsm 5113 Jun 12 13:53 plane_sprites.py
-rw-r--r-- 1 sgsm sgsm   14 Jun 12 14:23 print.py
-rw-r--r-- 1 sgsm sgsm  188 Jun 12 13:53 README.md
drwxr-xr-x 2 sgsm sgsm 4096 Jun 12 13:53 图片
[sgsm@centos python.github.io]$ 
```

## Git分支管理
```bash
# 创建分支
[sgsm@centos python.github.io]$ git branch  python
#  切换分支
[sgsm@centos python.github.io]$ git checkout  python
```
查看分支(默认不加参数会列出你在本地的分支)
```bash
[sgsm@centos python.github.io]$ git branch
* master
  python
[sgsm@centos python.github.io]$ 
```
主线和分支验证
```bash
[sgsm@centos sgsm]$ git checkout  master
Switched to branch 'master'
[sgsm@centos sgsm]$ ll
total 0
-rw-r--r-- 1 sgsm users 0 Jun 12 14:56 test
[sgsm@centos sgsm]$ 
[sgsm@centos sgsm]$ touch mihua                 # 在master上创建一个文件
[sgsm@centos sgsm]$ git add . 
[sgsm@centos sgsm]$ git  commit -m "mihua"    # 提交
[master a56e6c2] mihua
 1 file changed, 0 insertions(+), 0 deletions(-)
 create mode 100644 mihua
[sgsm@centos sgsm]$ ll                        # 在master可以看到
total 0
-rw-r--r-- 1 sgsm users 0 Jun 12 14:57 mihua
-rw-r--r-- 1 sgsm users 0 Jun 12 14:56 test
[sgsm@centos sgsm]$ 
[sgsm@centos sgsm]$ git checkout  trunk         # 切换到分支
Switched to branch 'trunk'
[sgsm@centos sgsm]$ ll                          # 没有那个文件了
total 0
-rw-r--r-- 1 sgsm users 0 Jun 12 14:56 test 
[sgsm@centos sgsm]$  git checkout  master          # 切换到master 就可以看到
Switched to branch 'master'
[sgsm@centos sgsm]$ ll
total 0
-rw-r--r-- 1 sgsm users 0 Jun 12 15:00 mihua
-rw-r--r-- 1 sgsm users 0 Jun 12 14:56 test
```

 git checkout -b (branchname) 命令来创建新分支并立即切换到该分支下，从而在该分支中操作
 ```bash
 略
 ```
删除分支
```bash
git branch -d (branchname)
```
例如我们要删除v0.6.10分支：
```bash
[sgsm@centos sgsm]$ git branch
* master
  trunk
  v0.6.10
[sgsm@centos sgsm]$ 
[sgsm@centos sgsm]$ git branch -d  v0.6.10
Deleted branch v0.6.10 (was a56e6c2).
[sgsm@centos sgsm]$ git branch
* master
  trunk
[sgsm@centos sgsm]$ 
```
合并分支
<br/>一旦某分支有了独立内容，你终究会希望将它合并回到你的主分支。 你可以使用以下命令将任何分支合并到当前分支中去：<br/>

```bash
git merge
```
例如：
```bash
[sgsm@centos sgsm]$ ll            # 新增finddelete.js   haha文件
total 4
-rw-r--r-- 1 sgsm users 66 Jun 12 15:58 finddelete.js
-rw-r--r-- 1 sgsm users  0 Jun 12 14:58 haha
-rw-r--r-- 1 sgsm users  0 Jun 12 14:56 test
[sgsm@centos sgsm]$ git  add . 
[sgsm@centos sgsm]$ git commit -m "add  finddelete.js"     # 提交 
[trunk c45f7e4] add  finddelete.js
 2 files changed, 6 insertions(+)
 create mode 100644 finddelete.js
 create mode 100644 haha
[sgsm@centos sgsm]$ git checkout master         #  切换到master   没有分支新提交的文件
Switched to branch 'master'
[sgsm@centos sgsm]$ ll
total 0
-rw-r--r-- 1 sgsm users 0 Jun 12 15:59 mihua
-rw-r--r-- 1 sgsm users 0 Jun 12 14:56 test
```
```bash
[sgsm@centos sgsm]$ git merge  trunk    # 把分支内容合并到主线
Merge branch 'trunk'

# Please enter a commit message to explain why this merge is necessary,
# especially if it merges an updated upstream into a topic branch.
#
# Lines starting with '#' will be ignored, and an empty message aborts
# the commit.
Merge made by the 'recursive' strategy.
 finddelete.js | 6 ++++++
 haha          | 0
 2 files changed, 6 insertions(+)
 create mode 100644 finddelete.js
 create mode 100644 haha
[sgsm@centos sgsm]$ ll
total 4
-rw-r--r-- 1 sgsm users 66 Jun 12 16:00 finddelete.js
-rw-r--r-- 1 sgsm users  0 Jun 12 16:00 haha
-rw-r--r-- 1 sgsm users  0 Jun 12 15:59 mihua
-rw-r--r-- 1 sgsm users  0 Jun 12 14:56 test
```

## Git查看提交历史
git log 命令查看提交历史：
```bash
[sgsm@centos sgsm]$ git log
commit 8c42560ed5c96cfa519b263a2ebd2eb8a85769a5 (HEAD -> master)
Merge: a56e6c2 c45f7e4
Author: long <942868591@qq.com>
Date:   Fri Jun 12 16:00:55 2020 +0800

    Merge branch 'trunk'

commit c45f7e446a702b3d267498d69d5b41b818a9aa85 (trunk)
Author: long <942868591@qq.com>
Date:   Fri Jun 12 15:58:38 2020 +0800

    add  finddelete.js

commit a56e6c2758dcdc6101a4b3d0b9c48fb2c8cedc07
Author: long <942868591@qq.com>
Date:   Fri Jun 12 14:58:00 2020 +0800

    mihua

commit 9831cb0d222df1f1ea18a32754001afa02036b79
Author: long <942868591@qq.com>
Date:   Fri Jun 12 14:57:06 2020 +0800

    test
[sgsm@centos sgsm]$ 
```
--oneline 选项来查看历史记录的简洁的版本
```bash
[sgsm@centos sgsm]$  git log --oneline
8c42560 (HEAD -> master) Merge branch 'trunk'
c45f7e4 (trunk) add  finddelete.js
a56e6c2 mihua
9831cb0 test
[sgsm@centos sgsm]$ 
```
--graph 选项，查看历史中什么时候出现了分支、合并。以下为相同的命令，开启了拓扑图选项：
```bash
[sgsm@centos sgsm]$  git log  --graph 
*   commit 8c42560ed5c96cfa519b263a2ebd2eb8a85769a5 (HEAD -> master)
|\  Merge: a56e6c2 c45f7e4
| | Author: long <942868591@qq.com>
| | Date:   Fri Jun 12 16:00:55 2020 +0800
| | 
| |     Merge branch 'trunk'
| | 
| * commit c45f7e446a702b3d267498d69d5b41b818a9aa85 (trunk)
| | Author: long <942868591@qq.com>
| | Date:   Fri Jun 12 15:58:38 2020 +0800
| | 
| |     add  finddelete.js
| | 
* | commit a56e6c2758dcdc6101a4b3d0b9c48fb2c8cedc07
|/  Author: long <942868591@qq.com>
|   Date:   Fri Jun 12 14:58:00 2020 +0800
|   
|       mihua
| 
* commit 9831cb0d222df1f1ea18a32754001afa02036b79
  Author: long <942868591@qq.com>
  Date:   Fri Jun 12 14:57:06 2020 +0800
  
      test
[sgsm@centos sgsm]$ 
```

 --reverse 参数来逆向显示所有日志
 ```bash
 [sgsm@centos sgsm]$  git log   --reverse    # 从第一次提交 -->最后一次提交
commit 9831cb0d222df1f1ea18a32754001afa02036b79
Author: long <942868591@qq.com>
Date:   Fri Jun 12 14:57:06 2020 +0800

    test

commit a56e6c2758dcdc6101a4b3d0b9c48fb2c8cedc07
Author: long <942868591@qq.com>
Date:   Fri Jun 12 14:58:00 2020 +0800

    mihua

commit c45f7e446a702b3d267498d69d5b41b818a9aa85 (trunk)
Author: long <942868591@qq.com>
Date:   Fri Jun 12 15:58:38 2020 +0800

    add  finddelete.js

commit 8c42560ed5c96cfa519b263a2ebd2eb8a85769a5 (HEAD -> master)
Merge: a56e6c2 c45f7e4
Author: long <942868591@qq.com>
Date:   Fri Jun 12 16:00:55 2020 +0800

    Merge branch 'trunk'
[sgsm@centos sgsm]$ 
```
 --author 查找指定用户的提交日志
 ```bash
 [sgsm@centos sgsm]$ git log --author=long --oneline -5     
8c42560 (HEAD -> master) Merge branch 'trunk'
c45f7e4 (trunk) add  finddelete.js
a56e6c2 mihua
9831cb0 test
[sgsm@centos sgsm]$ 
```
--since 和 --before 查看指定日期的日志 (--no-merges 选项以隐藏合并提交)
<br/>例如，如果我要看 Git 项目中三周前且在四月十八日之后的所有提交<br/>

```bash
git log --oneline --before={3.weeks.ago} --after={2010-04-18} --no-merges
```

## Git远程仓库(Github)
Git 并不像 SVN 那样有个中心服务器。
<br/>目前我们使用到的 Git 命令都是在本地执行，如果你想通过 Git 分享你的代码或者与其他开发人员合作。 你就需要将数据放到一台其他开发人员能够连接的服务器上。<br/>

添加远程库
<br/>要添加一个新的远程仓库，可以指定一个简单的名字，以便将来引用,命令格式如下：<br/>

```bash
git remote add [shortname] [url]
```

例如：
```bash
# 创建秘钥       -t  指定秘钥类型(默认就是rsa)  -P 可以指定密码   更详细的文档点击[]
[sgsm@centos sgsm]$ ssh-keygen 
```
```bash
# 查看秘钥并且把秘钥加到github的权限中
[sgsm@centos sgsm]$ cat  ~/.ssh/id_rsa.pub 
ssh-rsa AAAAB3NzaC1yc2EAAAABIwAAAQEA22A+usIGhnk0rhH7bcPTf6SptnuS70kmIWvqloFFhI8kK1srQTSAPR9EeR5O6VEXk/qJmbBNmr/0WoqmRRIVNGnttZwt8LUySTDBntu0peo9LX4WureKaptmZAIp/VXn2cYYkpdf1H1OYDFJ9rARmmBas3tSp/FlcdSxfgcofAq19VHtXnoJjcJSnh45GTsihtgp2nihHPBtScTqo3zf+xw2A4RWF11qCEm4Dmvvg4LPuMUSQHEhDMQG/VHmNBqlbFrUJieEcO99SL2/BIfqsqmxFsl6zDrNUj3Y2jB69Wcu4UT6DpY0mjw9rSY/KhyXk8SCwWetdt8AQDycCKzGuQ== sgsm@centos
```
github添加秘钥
<br/>![](../1.png)<br/>
![](../2.png)

为了验证是否成功，输入以下命令：
```bash
[sgsm@centos github]$ ssh -T git@github.com                         
The authenticity of host 'github.com (52.74.223.119)' can't be established.
RSA key fingerprint is 16:27:ac:a5:76:28:2d:36:63:1b:56:4d:eb:df:a6:48.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added 'github.com,52.74.223.119' (RSA) to the list of known hosts.
Hi xinlongOB/python.github.io! You've successfully authenticated, but GitHub does not provide shell access.
```



克隆仓库
```bash
[sgsm@centos github]$ git clone    https://github.com/xinlongOB/python.github.io.git
Cloning into 'python.github.io'...
remote: Enumerating objects: 46, done.
remote: Counting objects: 100% (46/46), done.
remote: Compressing objects: 100% (44/44), done.
remote: Total 46 (delta 7), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (46/46), done.
[sgsm@centos github]$ 
```
创建文件并且提交
```bash
[sgsm@centos python.github.io]$ echo "print("hello world")" > print.py
[sgsm@centos python.github.io]$ 
[sgsm@centos python.github.io]$ git add . 
[sgsm@centos python.github.io]$ git commit  -m "add print.py"
[master 23b3cd1] add print.py
 1 file changed, 1 insertion(+)
 create mode 100644 print.py
[sgsm@centos python.github.io]$ 
# 提交到 Github     因为这个github上面已经有origin   所以报错已存在  正常来说需要执行这一步
[sgsm@centos python.github.io]$ git remote add origin   https://github.com/xinlongOB/python.github.io.git
fatal: remote origin already exists.
# 推送到远程仓库
[sgsm@centos python.github.io]$ git push  origin master
Username for 'https://github.com': xinlongOB     # 第一次需要输入账号
Password for 'https://xinlongOB@github.com':      #  密码
Counting objects: 3, done.
Delta compression using up to 4 threads.
Compressing objects: 100% (2/2), done.
Writing objects: 100% (3/3), 284 bytes | 284.00 KiB/s, done.
Total 3 (delta 1), reused 0 (delta 0)
remote: Resolving deltas: 100% (1/1), completed with 1 local object.
To https://github.com/xinlongOB/python.github.io.git
   e4e3a59..23b3cd1  master -> master
[sgsm@centos python.github.io]$ 

删除远程仓库
```bash
git remote rm [别名]
```
例如：
```bash
git remote rm  origin
```