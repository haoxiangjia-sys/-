---
created: 2026-05-21
tags:
  - git
  - learning/note
---

# Git 笔记

创建仓库 git init
要用别人仓库东西 git clone + url
git init 的项目是未被跟踪的，如果要上传新版本要跟踪 ：git add <name>
不想被跟踪 git rm <name>
 git rm  --cache <name> 不被跟踪但是保留
 更改文件 后 git add <file-name>  设置成缓存状态
 git reset HESAD <name> 取消缓存状态
 git commit 提交此次修改
git status 查看状态
git diff       查文件状态
git log  看历史提交
git log --pretty=oneline  美化提交信息、
（本地仓库）

（远程仓库）
git remote add name + 链接 链接远程仓库
git remote 看仓库
git remote rename name  （重命名）
把代码推到远程仓库 git push name 分支

免密码登录：通过ssh 生成私钥和公钥

分支：包含hash值的文件（指向 一个提交对象的指针）可以在提交对象上建多个指针

git merge name 合并分支
git fetch 拉取分支
git -b

我现在在工作但是老板叫我修bug
plan 1 : 把现在写一半 的工作进行一个提交 git commit
plan 2 : 利用git stash 储藏我当前命令（用 git stash apply恢复）

reset
rebase 变基 
（git rebase -i head-3) 对前三次提交的一个分支进行编辑
