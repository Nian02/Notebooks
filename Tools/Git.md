Git 是一个分布式版本控制系统。
## Git 配置
可以保存 Git 用户名和电子邮件，这样就不必在以后的 Git 命令中再次输入它们。
在命令行中配置本地仓库的账号和邮箱：
```
git config --global user.name "Nian"  
git config --global user.email "1275102714@qq.com"  
```
**工作原理 / 流程**：
![[Git process.png]]

## 创建本地库
1. **初始化工作区**
```
git init
```
当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的
2. **文件添加到暂存区**
```
git add readme.txt
```
当添加所有的文件时：
```
git add .
```
3. **文件提交到仓库**
```
git commit -m "wrote a readme file"
```
-m 后面是你修改的指令说明，这样你就能从历史记录里方便地找到改动记录。
4. **查看是否有文件未提交**
```
git status
```

## 添加远程仓库GitHub
### 设置ssh key
1. **获取ssh key**

在git Bash中输入
```
ssh-keygen -t rsa -C "1275102714@qq.com"
```
一路回车，使用默认值即可。
在user主目录里找到.ssh目录，里面有**id_rsa和id_rsa.pub**两个文件。
- id_rsa是私钥，不能泄露出去。
- id_rsa.pub是公钥，公钥可以放心地告诉任何人。

2. **绑定ssh key**

GitHub->settings->SSH Keys->Title+Key(id_rsa.pub内容)

### 关联本地版本库和远程库
1. **关联**

将本地仓库和远程仓库进行关联，并将远程仓库命名为`origin` 
```
git remote add origin "https"
```
**不能同时连接多个远程仓库**，要修改：
```
git remote rm origin
git remote add ...
```

2. **推送**

将本地仓库中的内容推送到远程仓库`origin`中。 
```
git push -u origin master
```
第一次提交使用`-u`
注意内容在master分支下

### 从远程仓库克隆
```
git clone 
```
## 分支

一开始的时候，master分支是一条线，Git用master指向最新的提交，再用HEAD指向master，就能确定当前分支，以及当前分支的提交点。

1. **查看分支**
```
git branch
```
2. **创建分支**
```
git branch branchname
```
3. **切换分支**
```
git checkout branchname
```
4. **合并某分支到当前分支**
```
git merge branchname
```
通常合并分支时，git一般使用”Fast forward”模式
5. **删除分支**
```
git branch –d branchname
```

## GIT的版本回退
1. **提交版本**
```
git log
```
我们可以看到每次提交的时候都干了什么，以及每个版本的版本号

2. **回退版本**

HEAD来表示当前的版本
回退到上一个版本：
```
git reset --hard HEAD^
```
若是版本号很多，则
```
git reset  --hard HEAD~100
```
Git在内部有个指向当前版本的HEAD指针，当你回退版本的时候，Git仅仅是把HEAD从指向回滚到的版本。

3. **利用版本号回退**
```
git reset  --hard 版本号
```
获取版本号
```
git reflog
```

## 管理修改
**Git跟踪并管理的是修改，而非文件**。

### 撤销修改
在工作区修改过了文件想撤销：
1. 回退到上个版本[[Git#GIT的版本回退]]
2. 丢弃工作区`file`的修改
```
git checkout  -- file
```

