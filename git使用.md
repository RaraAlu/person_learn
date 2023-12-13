```shell
//全局初始化
git config  --global user.name "chenlusheng"
git config --global user.email "zhangsan.com"
git config --list

//初始化
git init
//查看状态
git status
//添加文件到缓存区
git add 文件名 
git add .  //解析.gitignore文件内容
git add *  //不解析.gitignore文件内容
//提交
git commit -m "注释"   //单引号和双引号有区别，单引号会作为注释内容
//查看日志
git log
git log --pretty=oneline //单行显示日志
//查看当前修改的内容
git diff 文件名

//版本回退(回滚)
//在windows的cmd控制台下操作git，想要回滚到上一次提交，
//但是输入git reset --hard HEAD^后就显示more?，
//多按几次回车后就报错如下，如何解决呢？
//这是因为cmd控制台中换行符默认是^，而不是\ ，所以它的more？
//的意思是问你下一行是否需要再输入，而^ 符号就被当做换行符而被git命令忽略掉了
解决方法有如下几种：
加引号：git reset --hard "HEAD^"
加一个^：git reset --hard HEAD^^
换成~：git reset --hard HEAD~ 或者 git reset --hard HEAD~1
~ 后面的数字表示回退几次提交，默认是一次

git reflog
git reset --hard fea3a8a

//修改和删除
//还没有提交到缓存
git checkout 1.txt

//对于已经提交的缓存区
git rm -rf 1.txt
git commit -m "xxx"
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
ssh-keygen -t rsa -b 4096 -C "zhangsanjh42@126.com" 
>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>
//创建分支
git branch debug
//查看分支
git branch
//切换分支
git checkout master
//创建分支并且切换
git checkout -b test
//合并分支(先要切换到目标分支)
git merge test
//如果出现了两个分支都修改同一个文件，合并过程会有冲突(手动解决，然后提交)
git branch -r //查看远程所有分支
git branch -a //查看本地和远程的所有分支
git branch -d <branchname> //删除本地分支
git branch -d -r <branchname> //删除远程分支，删除后还需推送到服务器
git push origin:<branchname>  //删除后推送至服务器
git branch -m <oldbranch> <newbranch> //重命名本地分支
//d --delete：删除
//D--delete --force的快捷键
//f--force：强制
//m--move：移动或重命名
//M--move --force的快捷键
//r--remote：远程
//a--all：所有

//隐藏修改的但是还没有添加到缓存区的内容
git stash
//查看列表
git stash list
//删除列表
git stash drop 
//恢复列表内容
git stash apply

git reset head~ --soft

//远程仓库github
//获取已存在的项目
git clone https://gitee.com/LuIrara/git_test.git
//制作整数
C:\Program Files\Git\usr\bin\ssh-keygen -t rsa -C '你的邮箱名'
//生成的文件在C:\Users\你的用户名\.ssh 目录下
//初次上传需要绑定
git remote add origin https://gitee.com/LuIrara/git_test.git
git remote add origin https://github.com/RaraAlu/lusheng_bot.git
git remote add origin https://github.com/RaraAlu/ROS-.git

git remote rename
//上传分支
git push -u origin master

//查看更新
git remote show origin
//获取更新
git pull//git pull <远程主机名> <远程分支名>:<本地分支名>

git fetch <远程主机名> //这个命令将某个远程主机的更新全部取回本地
//如果只想取回特定分支的更新，可以指定分支名：
git fetch <远程主机名> <分支名> //注意之间有空格
git fetch origin master //从远程主机的master分支拉取最新内容 
git merge FETCH_HEAD    //将拉取下来的最新内容合并到当前所在的分支中

//使用Gitee，如果不能提交代码
git config --global --unset credential.helper
git config --system --unset credential.helper
git config --system credential.helperl store
```

```
>码云私人令牌
76297b67460a72e3f4ab2657a3bc7bcf
>github令牌
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
```

