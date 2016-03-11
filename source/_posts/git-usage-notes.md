title: "Git 使用汇总"
date: 2013-01-11
tags: git
categories: 技术
---

### git 基本操作命令

git init 用于初始化一个git仓库
git config user.name "buzheng" 配置用户名
git config user.email "zany@buzheng.org"
git remote add origin git@xxxx.com:xxxxx_project/xxxx.git 添加远程服务端源
git fetch 获取代码
git pull 获取代码，并进行了merge操作
git add -A 添加所有修改的文件到 index
git commit -a -m "message" 提交到本地仓库
git push 提交到远程服务器
git log 查看提交记录，好像有参数 -p
git status 查看当前工作目录的状态，是否有变更等
git diff 查看工作目录与index的不同
git diff –cached 查看index与commit的不同

git branch 查看所有分支，以及当前所处的分支
git branch name 创建分支name
git checkout name 切换到分支name
git branch -d name 删除分支，安全删除，如果分支没有被提交或者合并，是不能删除的
git branch -D name 删除分支，强制删除
git merge name 合并分支

### Git 服务提交时返回 Non-fast-forward 错误

产生这个错误的问题是由于服务端已经存在和你本地库里冲突的版本，或者是你已经获取了最新的版本，但是还没有合并。
使用命令 git pull 或者 git fetch 和 git merge 后再 git push 就没问题了。
从这里可以得出 git pull = git fetch & git merge