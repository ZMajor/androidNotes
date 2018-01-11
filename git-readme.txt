学习地址：https://www.liaoxuefeng.com/

1. 安装配置
	$ git config --global user.name "Your Name"
	$ git config --global user.email "email@example.com"

	/*注意git config命令的--global参数，用了这个参数，表示你这台机器上所有的Git仓库都会使用这个配置，
	当然也可以对某个仓库指定不同的用户名和Email地址。*/



2. 创建版本库
	初始化一个Git仓库：git init
	提交：先add再commit, 
	$ git add file1.txt
	$ git add file2.txt file3.txt
	$ git commit -m "add 3 files."



提交当前所有文件：$ add . 
查看文件内容：cat readme.txt 
查看修改状态：git status 
查看修改内容：git diff
查看提交日志：git log （$ git log --pretty=oneline 简化日志, 以便确定要回退到哪个版本）
回退上一个版本：git reset --hard HEAD^ 
指定版本ID回退：git reset --hard commit_id（前7位）
查看命令历史：git reflog （以便确定要回到未来的哪个版本）



撤销修改：git checkout -- readme.txt
撤销已添加修改：先git reset HEAD readme.txt 再 git checkout -- readme.txt
撤销已提交修改：git reset --hard commit_id（前7位）



工作区删除文件：$ rm test.txt
版本库删除文件：先$ git rm test.txt 再$ git commit -m "remove test.txt"
恢复工作区删除的文件：$ git checkout -- test.txt


1. 使用SSH Key （C盘用户根目录.ssh文件夹的id_rsa和id_rsa.pub这两个文件）
		没有则创建：ssh-keygen -t rsa -C "385994264@qq.com"
		输入文件名: id_rsa （Enter file ... ）
		输入密码：（Enter passphrase... 不需要密码直接回车或输入你在github上设置的密码）
2. 登录Github,打开“Account settings”,点“Add SSH Key”
3. 填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容
4. 测试：ssh -T git@github.com，输入yes,看见 hi ... 说明成功
5. 修改.git文件夹下config中的url, url=git@github.com:...


要关联一个远程库：（通过ssh支持的原生git协议速度最快）
1. git remote add origin git@server-name:path/repo-name.git；
2. git push -u origin master 第一次推送master分支的所有内容；
此后推送最新修改:git push origin master 或 git push

要关联多个远程库：
1. 移除现在旧有的远程服务器origin：git remote rm origin
2. 关联gitee远程库: git remote add gitee git@gitee.com:Zmajor/androidNotes.git 
3. 关联github远程库: git remote add github git@github.com:ZMajor/androidNotes.git
4. 查看远程库信息：git remote -v

推送到指定远程库github的主分支master：git push github master
从远程库更新：git pull


从远程库下载关联：
 $ git clone https://github.com/ZPJay/Garbage.git

进入文件目录：$ cd 目录 
查看文件列表：$ ls

创建：$ git branch dev
切换：$ git checkout dev
创建并切换分支：$ git checkout -b dev

查看分支：git branch
合并分支到当前分支 ：$ git merge dev
删除分支：$ git branch -d dev
查看分支合并图：$ git log --graph --pretty=oneline --abbrev-commit


分支合并--no-ff参数: $ git merge --no-ff -m "merge with no-ff" dev
表示禁用Fast forward，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。


dev修改到一半， 需要去修改最新的BUG。
1.保存工作现场：git stash
2.切回需要修改的分支并创建修改分支：git checkout master	, git checkout -b issue-101
3.在bug分支上修改并提交：git add readme.txt ， git commit -m "fix bug 101"
4.切回master分支并完成合并: git merge --no-ff -m "merged bug fix 101" issue-101
5.最后删除issue-101分支：git checkout master ，git branch -d issue-101
6.接着回到dev分支干活并查看保存的工作现场：git checkout dev，git stash list
7.两张恢复方法： 
	1.先恢复再删除stash内容： git stash apply，git stash drop   
	2.恢复的同时把stash内容也删：git stash pop
8. 可以多次stash内容，先用git stash list查看，然后恢复指定的stash，git stash apply stash@{0}

删除还没有被合并的分支，需要强行删除:git branch -D dev

多人协作的工作模式：
查看远程库信息，使用git remote -v；
本地新建的分支如果不推送到远程，对其他人就是不可见的；
从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。


标签：
新建标签：git tag v1.0 （默认最新的提交）
查看标签：git tag
指定commitId标签：
	1.查看历史提交记录：git log --pretty=oneline --abbrev-commit（简化）
	2.新建commitId标签：git tag v0.9 6224937
查看标签信息：git show v0.9

带有说明的标签：$ git tag -a v0.1 -m "version 0.1 released" 3628164（-a指定标签名，-m指定说明文字）

删除标签：git tag -d v0.1
推送标签到远程：git push origin v1.0（指定）    git push origin --tags（所有未提送的）
删除远程标签：（先删本地再删远程）  git tag -d v0.9 , git push origin :refs/tags/v0.9

忽略某些文件时，需要编写.gitignore(文本编辑器里“保存”或者“另存为”就可以把文件保存为.gitignore了)
.gitignore文件本身要放到版本库里，并且可以对.gitignore做版本管理

配置别名：
$ git config --global alias.st status
$ git config --global alias.co checkout
$ git config --global alias.ci commit
$ git config --global alias.br branch
$ git config --global alias.unstage 'reset HEAD'
$ git config --global alias.last 'log -1'

$ git config --global alias.lg "log --color --graph --pretty=format:'%Cred%h%Creset -%C(yellow)%d%Creset %s %Cgreen(%cr) %C(bold blue)<%an>%Creset' --abbrev-commit"

而当前用户的Git配置文件放在用户主目录下的一个隐藏文件.gitconfig中
配置别名也可以直接修改这个文件，如果改错了，可以删掉文件重新通过命令配置。



===========

在查看修改历史时，对于中文文件名，git log和gitk都会出现类似的乱码：
sepg\344\274\232\350\256\256\346\200\273\347\273\223.doc

通过看git的源码，找到了解决方案：

git config core.quotepath false

core.quotepath设为false的话，就不会对0x80以上的字符进行quote。中文显示正常