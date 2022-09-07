---
title: Git Flow分支管理
date: 2022-09-07 17:18:24
toc: true
---

## 两种核心分支
1、主分支(`main/master`): 代码库应该有一个、且仅有一个主分支。所有提供给用户使用的正式版本，都在这个主分支上发布。这个分支只能从其他分支合并，不能在这个分支上直接修改。需要注意的是，**所有在主分支上的提交都应该标记tag**。

2、开发主分支(`develop`): 这个分支是我们是我们的主开发分支，包含所有要发布到下一个`Release`的代码，这个主要合并与其他分支，比如`Feature`分支。该分支应该只是进行一些优化和升级开发，如果有新的需求应该拉出一个`feature`分支。

## 三种临时分支
1、功能(`feature`)分支：这个分支主要用来**开发新功能**，一旦开发完成，我们合并回`develop`分支。

2.、预发布(`release`)分支：当你需要一个发布一个新`Release`的时候，我们基于`Develop`分支创建一个`Release`分支，完成`Release`后，我们合并到`Master`和`Develop`分支。

3、修补Bug(hotfix)分支：当我们在生产环境发现新的Bug时候，我们需要创建一个`Hotfix`, 完成`Hotfix`后，我们合并回`Master`和`Develop`分支，所以`Hotfix`的改动会进入下一个`Release`。

这三种分支都属于临时性需要，使用完后，应该删除，使得代码库的常设分支始终只有master和develop分支.

## Git Flow流程示例代码
### 1、新建本地仓库、远程仓库，将主分支推送到远程库上
```bash
# 本地文件夹
git init # 初始化本地仓库
# 修改项目内容后
git add . # 提交到暂存区
git commit -m "xxx" # 提交到本地仓库
git branch -M main #将主分支名从master改为main

# github上新建仓库

# 创建远程库别名
# git remote add 别名 远程地址
git remote add origin git@github.com:gzdl-strive/xxxx.git
# 将本地主分支推送到远程库上
git push -u origin main
```

### 2、创建开发主分支，将开发主分支推送到远程库上
```bash
# 本地仓库中创建develop分支
# 从main主分支中拉出develop分支
# git branch -v => 查看分支
# git checkout 分支名 => 切换分支
# git checkout -b 分支吗
git checkout -b develop main # 从main分支中拉出develop分支，并切换到develop分支上

# 发布develop分支到远程库
git push -u origin develop
```

### 3、创建feature分支开发，开发完成后合并到develop分支
```bash
# 本地仓库
# 从develop拉出feature_v1.0功能分支
git checkout -b feature_v1.0 develop
# 发布feature_v1.0分支到远程库
git push -u origin feature_v1.0

# 在feature_v1.0上开发功能
# 完成feature_v1.0的开发

# 合并到develop分支
# 先从develop上获取最新
git pull origin develop
# 切换到develop分支
git checkout develop
# 从feature_v1.0分支合并到develop分支
git merge --no-ff feature_v1.0
# 删除feature_v1.0分支
git branch -d feature_v1.0
# 推送develop最新内容到远程develop分支上
git push -u origin develop
# 删除远程库上的feature_v1.0分支
# 直接在界面上点击branches,点击删除
```
