Git 是一个分布式版本控制系统。
## Git 配置
可以保存 Git 用户名和电子邮件，这样就不必在以后的 Git 命令中再次输入它们。
在命令行中配置本地仓库的账号和邮箱：
```
git config --global user.name "Nian"  
git config --global user.email "1275102714@qq.com"  

```
工作原理 / 流程：
![[Git process.png]]

## 添加远程仓库GitHub
### 设置ssh key
1. **获取ssh key**
在git Bash中输入
```
ssh-keygen -t rsa -C "1275102714@qq.com"
```
一路回车，使用默认值即可。
在user主目录里找到.ssh目录，里面有id_rsa和id_rsa.pub两个文件，这两个就是SSH Key的秘钥对，id_rsa是私钥，不能泄露出去，id_rsa.pub是公钥，公钥可以放心地告诉任何人。

2. **绑定ssh key**
登陆GitHub，点击头像下面的“settings”，选择“SSH Keys：，填上任意Title，在Key文本框里粘贴id_rsa.pub文件的内容（用文本打开就可以），就完成啦。

3. 

## 创建版本库
1. **初始化工作区**
```
git init
```
当前目录下多了一个.git的目录，这个目录是Git来跟踪管理版本库的
2. **文件添加到仓库**
```
git add readme.txt
```
3. **文件提交到仓库**
```
git commit -m “wrote a readme file
```
-m 后面是你修改的指令说明，这样你就能从历史记录里方便地找到改动记录。
4. **查看是否有文件未提交**
```
git status
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
当你同一个文件提交两次，第一次提交的git add了，第二次的没有（第一次修改被放入暂存区，第二次没有）。你commit之后会发现只有第一次修改的被提交了。


