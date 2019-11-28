## 分支的分类
- Feature Branch 
- Release Branch
- Hotfix Branch


## 新建一个dev分支，并提交到远程
- git checkout dev
- git branch 查看是否在dev分支上
- git add .
- git commit -m "注释"
- git branch --set-upstream-to=origin/dev
- git push origin dev

## 新建一个feature分支，修改代码后，合并到dev分支并提交到远程
- git checkout -b myfeature dev
- 修改代码并且add 和commit之后，切换到dev分支 git checkout dev
- 将myfeature分支合并到dev分支 git merge --no-ff myfeature
- 删除myfeature分支  git brach -d myfeature
- 提交到远程dev分支 git push origin dev


#### 查看各个分支最后一次提交记录
git branch -v

#### 查看已合并和未合并的分支
git branch --merged 和 git branch --no-merged


未合并的分支删除时会报错，如果需要强制删除，请使用-D参数
git branch -D hotfix














