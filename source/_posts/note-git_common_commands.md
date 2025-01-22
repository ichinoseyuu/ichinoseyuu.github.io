---
title: git常用命令汇总
date: 2024-10-25 12:58:39
description:  汇总了一些常用的git命令
categories: 笔记
cover: https://cdn.jsdelivr.net/gh/ichinoseyuu/Image/202501230052010.png
tags: 
    - git

---

---
git命令：

```bash
git init # 初始化仓库
git status # 查看仓库状态
git log # 查看提交历史
git add .  # 添加所有文件到暂存区
git commit -m "commit message"  # 提交暂存区文件到本地仓库
git remote add origin <repository>  # 添加远程仓库
git pull origin master  # 拉取远程仓库到本地仓库
git pull --rebase origin master  # 拉取远程仓库并合并到本地仓库
git push origin master  # 推送本地仓库到远程仓库

git clone <repository>  # 克隆远程仓库
git branch # 查看分支
git checkout <branch> # 切换分支
git merge <branch> # 合并分支
git branch -d <branch> # 删除分支
git branch -D <branch> # 强制删除分支
git reset --hard HEAD # 回退到上一个版本
git reset --hard <commit> # 回退到指定版本
git rm --cached images/ # 删除文件索引，下次提交不会上传
git rm -r <file/dir> # 删除文件索引及本地文件
```
