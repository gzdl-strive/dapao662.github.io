---
title: Git笔记
toc: true
date: 2021-11-15 09:39:24
---

## Git常用命令

### 1、设置用户签名
```powershell
git config --global user.name 用户名
git config --global user.email 设置用户签名
```

说明：签名的作用是区分不同操作者身份。用户的签名信息在每一个版本的提交信息中能够看到，以此确认本次提交是谁做的。**Git首次安装必须设置一下用户签名，否则无法提交代码**

**注意：这里设置用户签名和将来登录GitHub（或其他代码托管中心）的账号没有任何关系**

### 2、初始化本地仓库
```powershell
git init
```

初始化后工作区会多出一个.git文件夹，在git bash中输入ll，查看不到.git文件夹，这时可以输入ll -a，或者进入.git文件夹中输入ll命令

### 3、查看本地库状态
```powershell
git status
```

git bash中新建文件
vim hello.txt( 按i 插入，esc退出插入，p整行复制， :wq保存退出)
tail -n 1 hello.txt(查看最后一行)

### 4、添加暂存区
```powershell
# 把xxx文件添加到暂存区
git add xxx

# 把所有文件添加到暂存区
git add .
```

### 5、提交本地库
```powershell
git commit -m "日志信息" 文件名

# 查看引用日志信息
git reflog 

# 查看详细日志信息
git log
```

### 6、版本穿梭
```powershell
git reflog 查看各个版本的版本号
使用git reset -- hard 版本号
```
可以通过查看 .git 文件夹下 refs文件夹下的heads下的master文件,可以查看当前所指向的版本号

## Git分支操作
在版本控制过程中，同时推进多个任务，为每个任务，我们就可以创建每个任务的单独分支。使用分支意味着程序员可以把自己的工作从开发主线上分离开来，开发自己分支的时候，不会影响主线分支的运行。对于初学者而言，分支可以简单理解为副本，一个分支就是一个单独的副本。(分支底层其实也是指针的引用)

**分支操作**
```powershell
git branch 分支名  =>  创建分支
git branch -v  => 查看分支
git checkout 分支名 => 切换分支
git merge 分支名 => 把指定的分支合并到当前分支上
```

## 远程仓库操作
```powershell
git remote -v  =>  查看当前所有远程地址别名
git remote add 别名 远程地址  =>  起别名
git push 别名 分支  =>  推送本地分支上的内容到远程仓库
git clone 远程地址  => 将远程仓库的内容克隆到本地(1、拉取代码 2、初始化本地仓库 3、创建别名（自动帮我们把别名命名为origin）)
git pull 远程库地址别名 远程分支名 => 将远程仓库对于分支最新内容拉下来后与当前本地分支直接合并
```

## 团队协作
把github上的代码拉取下来了，做了修改，但是不能提交到远程库上，需要创建远程库的人，对该远程库进行设置，邀请需要提交代码的人，github/git-demo库下，settings -> Manage access -> add people

输入要邀请的github账号后，会生成一个地址，把地址发送给被邀请人，被邀请人在登录自己github账号后，复制该地址到地址栏，就可以看到创建远程库的人的要求，可以进行接收或拒绝

## 跨团队协作
A(创建github仓库人) -> B(外界人员)
A -> 把仓库地址发送给 B -> B接收到地址后 如果想要修改A中的代码，那么需要fork该仓库到自己的github上
B -> 在自己的github上修改了代码(可以直接在github上修改，或者本地把项目克隆下去，修改后提交到github上)
B 想要把自己修改后的代码提交到A的仓库上，需要创建Pull request(B --> Pull requests -> New Pull reqeust -> Create pull request)

A 就可以在自己的仓库中的pull request上看到B提交的pull request,可以对B修改的代码进行review,后进行merge
![跨团队协作](/assets/codeManagerImg/pullRequest.png "跨团队合作")


## SSH免密登录
`C:\Users\asus`下有个.ssh目录
通过运行`ssh-keygen -t rsa -C xxxxxxx@qq.com`
（ssh-keygen: 生成ssh公钥私钥免密登录的命令）
（-t: 指定用哪种加密算法来生成）
（rsa：使用rsa（非对称加密协议）算法）
（-C:一种描述）
生成.ssh目录，我们需要该目录下的公钥,即以.pub结尾的文件，获取到公钥后，我们登录github -> settings -> SSH and GPG keys -> New SSH key，把公钥添加进去
 这样我们以后就可以通过SSH来拉取/推送到github

 