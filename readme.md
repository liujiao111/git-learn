
# GIT基础以及本地版本管理
### git设置用户名和邮箱：
```
git config --global user.name "Your Name"
git config --global user.email "email@example.com"
```


注意：版本控制系统，其实只能跟踪文本文件的改动，比如TXT文件，网页，所有的程序代码等等，它可以告诉你每次的改动，比如在第5行加了一个单词“Linux”，在第8行删了一个单词“Windows”。而图片、视频这些二进制文件，虽然也能由版本控制系统管理，但没法跟踪文件的变化，只能把二进制文件每次改动串起来，也就是只知道图片从100KB改成了120KB，但到底改了啥，版本控制系统不知道，也没法知道。

如果要真正使用版本控制系统，就要以纯文本方式编写文件。

### 添加文件并提交到本地仓库：
```
git add file1.txt
git add file2.txt file3.txt
git commit -m "add 3 files."
```

###  掌握工作区的状态
使用`git status`命令告诉你有文件被修改过
```
git status
```
使用 git diff 命令查看某个文件被修改的内容
```
git diff readme.txt 
```


### git log命令显示从最近到最远的提交日志
```
git log
```

如果嫌输出信息太多，看得眼花缭乱的，可以试试加上--pretty=oneline参数：
```
git log --pretty=oneline
```

### 回退到某一个版本：
```
git reset --hard HEAD"^"
```

Git必须知道当前版本是哪个版本，在Git中，用HEAD表示当前版本，也就是最新的提交1094adb...（注意我的提交ID和你的肯定不一样），上一个版本就是HEAD^，上上一个版本就是HEAD^^，当然往上100个版本写100个^比较容易数不过来，所以写成HEAD~100。

```
需要注意的是，在mac或linux上HEAD 后面的尖号不需要加引号，windows上需要，否则无法执行命令。
```

也可以使用 `git reset --hard + 版本号`   来回退到某一个指定版本，这个可以回退到最新的版本，如：
```
git reset --hard d3fc2
```

版本号不用写完整，前几位就行。

### 要重返未来，用`git reflog`查看命令历史，以便确定要回到未来的哪个版本。
```
git reflog
```

### 工作区和暂存区
工作区就是电脑里的文件夹‘
版本库：工作区有一个隐藏目录.git，这个不算工作区，而是Git的版本库。
版本库包括：
- 暂存区;
- Git为我们自动创建的第一个分支master;
- 以及指向master的一个指针叫HEAD。

git add 命令实际上就是把文件修改添加到暂存区，
而git commit 就是把暂存区的所有内容提交到当前分支。

### 提交后，用git diff HEAD -- readme.txt命令可以查看工作区和版本库里面最新版本的区别：
```
git diff HEAD -- readme.txt
```

### git checkout -- file可以丢弃工作区的修改，让这个文件回到最近一次git commit或git add时的状态
```
git checkout -- readme.txt
```

注意：git checkout -- file命令中的--很重要，没有--，就变成了“切换到另一个分支”的命令，我们在后面的分支管理中会再次遇到git checkout命令。

### add到暂存区的文件如何撤销修改：git reset HEAD <file>
```
git reset HEAD readme.txt
```
然后 丢弃工作区的修改：
```
git checkout -- readme.txt
```


小结：
当你改乱了工作区某个文件的内容，想直接丢弃工作区的修改时，用命令`git checkout -- file`。
当你不但改乱了工作区某个文件的内容，还添加到了暂存区时，想丢弃修改，分两步，第一步用命令`git reset HEAD <file>`，就回到了场景1，第二步按场景1操作。
已经提交了不合适的修改到版本库时，想要撤销本次提交，参考版本回退一节，不过前提是没有推送到远程库。
### 删除文件：有一个文件，已经add到版本库并commit到分支了，但是在本地把该文件删除了。现有两种情况：
确实要从版本库中删除该文件，那就用命令git rm删掉，并且git commit：
```
git rm test.txt
```

另一种情况是删错了，因为版本库里还有呢，所以可以很轻松地把误删的文件恢复到最新版本：
```
git checkout -- test.txt
```

git checkout其实是用版本库里的版本替换工作区的版本，无论工作区是修改还是删除，都可以“一键还原”。

# 远程仓库
### 本地仓库关联远程仓库：
```
git remote add origin git@server-name:path/repo-name.git；
```

origin 是远程库的名字就是origin，这是Git默认的叫法，也可以改成别的
### 推送本地提交到远程仓库：
```
git push -u origin master
```

用git push命令，实际上是把当前分支master推送到远程。
由于远程库是空的，我们第一次推送master分支时，加上了-u参数，Git不但会把本地的master分支内容推送的远程新的master分支，还会把本地的master分支和远程的master分支关联起来，在以后的推送或者拉取时就可以简化命令。

此后，每次本地提交后，只要有必要，就可以使用命令git push origin master推送最新修改；
```
git push origin master
```


### 仓库克隆
要克隆一个仓库，首先必须知道仓库的地址，然后使用git clone命令克隆。
Git支持多种协议，包括https，但通过ssh支持的原生git协议速度最快。
```
git clone <远程仓库地址>
```

# 分支管理

## 分支创建以及合并
### 创建分支，并切换到刚创建的分支：
```
git checkout -b dev
```

git checkout命令加上-b参数表示创建并切换 ，相当于 `git branch dev` 和 `git checkout dev` 两条命令的组合。

###	查看分支：
```
git branch
```

git branch命令会列出所有分支，当前分支前面会标一个*号。

### 合并dev分支的内容到master 分支：
先切换到master分支，再执行下面语句
```
git merge dev
```

git merge命令用于合并指定分支到当前分支

### 合并后删除dev分支：
```
git branch -d dev
```

小结：
- 查看分支：git branch
- 创建分支：git branch <name>
- 切换分支：git checkout <name>
- 创建+切换分支：git checkout -b <name>
- 合并某分支到当前分支：git merge <name>
- 删除分支：git branch -d <name>

### 解决冲突
git log也可以看到分支的合并情况：
```
git log --graph --pretty=oneline --abbrev-commit
```

当Git无法自动合并分支时，就必须首先解决冲突。解决冲突后，再提交，合并完成。
解决冲突就是把Git合并失败的文件手动编辑为我们希望的内容，再提交。

## 分支管理策略
### 合并dev分支，强制禁用Fast forward模式，Git就会在merge时生成一个新的commit，这样，从分支历史上就可以看出分支信息。
```
git merge --no-ff -m "merge with no-ff" dev
```

首先，master分支应该是非常稳定的，也就是仅用来发布新版本，平时不能在上面干活；
那在哪干活呢？干活都在dev分支上，也就是说，dev分支是不稳定的，到某个时候，比如1.0版本发布时，再把dev分支合并到master上，在master分支发布1.0版本；

## Bug分支
### 把当前工作现场“储藏”起来，等以后恢复现场后继续工作：
```
git stash
```
###  查看工作现场：
```
git stash list
```

### 恢复暂存的内容：
git stash apply恢复，但是恢复后，stash内容并不删除，你需要用`git stash drop`来删除；
另一种方式是用`git stash pop`，恢复的同时把stash内容也删了：

你可以多次stash，恢复的时候，先用`git stash list`查看，然后恢复指定的stash，用命令：
```
git stash apply stash@{0}
```

##Feature 分支：
## 开发一个新feature，最好新建一个分支；
### 如果要丢弃一个没有被合并过的分支，可以通过git branch -D <name>强行删除。
```
git branch -D feature-vulcan
```

##v多人协作
### 查看远程库的信息
```
git remote
```

或者，用`git remote -v`显示更详细的信息：
###  推送分支：
```
git push origin master
```

果要推送其他分支，比如dev，就改成
```
git push origin dev
```

master分支是主分支，因此要时刻与远程同步；
dev分支是开发分支，团队所有成员都需要在上面工作，所以也需要与远程同步；
bug分支只用于在本地修复bug，就没必要推到远程了，除非老板要看看你每周到底修复了几个bug；
feature分支是否推到远程，取决于你是否和你的小伙伴合作在上面开发。

### 推送时候有冲突：先pull最新代码，解决冲突后，再push:
```
git pull
```

如果git pull失败了，原因是没有指定本地dev分支与远程origin/dev分支的链接，根据提示，设置dev和origin/dev的链接：
```
git branch --set-upstream-to=origin/dev dev
```
 然后再pull就OK

小结
- 查看远程库信息，使用git remote -v；
- 本地新建的分支如果不推送到远程，对其他人就是不可见的；
- 从本地推送分支，使用git push origin branch-name，如果推送失败，先用git pull抓取远程的新提交；
- 在本地创建和远程分支对应的分支，使用git checkout -b branch-name origin/branch-name，本地和远程分支的名称最好一致；
- 建立本地分支和远程分支的关联，使用git branch --set-upstream branch-name origin/branch-name；
- 从远程抓取分支，使用git pull，如果有冲突，要先处理冲突。




## 标签管理
发布一个版本时，我们通常先在版本库中打一个标签（tag），这样，就唯一确定了打标签时刻的版本。将来无论什么时候，取某个标签的版本，就是把那个打标签的时刻的历史版本取出来。所以，标签也是版本库的一个快照。
### 打新标签 git tag <name>
```
git tag v1.0
````

### 查看所有标签 git tag
```
git tag
```

默认标签是打在最新提交的commit上的，如果要对之前的commit打标签，只需要找到历史提交的comit id,  git tag <name> <commit id>就可以了
### 找到历史提交记录
```
git log --pretty=oneline --abbrev-commit   
```

###  对某个commit打标签
```
git tag v0.9 f52c633
```

### 查看所有标签列表
```
git tag
```
### 查看某个标签信息  git show <tagname>
 ```
 git show v0.9
```

创建带有说明的标签，用-a指定标签名，-m指定说明文字：
```
git tag -a v0.1 -m "version 0.1 released" 1094adb
```


注意：标签总是和某个commit挂钩。如果这个commit既出现在master分支，又出现在dev分支，那么在这两个分支上都可以看到这个标签。


### 删除标签：
```
git tag -d v0.1
```

### 推送本地标签到远程：
```
git push origin v1.0
```

### 一次性推送全部尚未推送到远程的本地标签：
```
git push origin --tags
```

### 删除远程标签：
- 先从本地删除
```
git tag -d v0.9
```
- 再远程删除
```
git push origin :refs/tags/v0.9
```


附：
解决GitHub每次push时都提示输入用户名和密码的问题：https换成ssh方式，参考：https://blog.csdn.net/mr_javascript/article/details/83043174

