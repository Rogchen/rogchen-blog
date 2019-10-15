title: git命令学习
tags:
	[git,linux]

---
随着git越来越火，学习好git的常规操作是很有必要。我们从本地->远程，使用https到使用ssh来介绍使用git
# 前言：git的初始化配置
### 1、创建文件夹
`mkdir 文件名`
### 2、初始化
初始化本地仓库（.git目录）
存放的是本地库配置信息，不要胡乱删除修改。
`git init`

<!--more-->
### 3、设置签名
>3.1 形式： 
>> 用户名：rogchen
>> email地址：c****@163.com
>作用：区分不同开发人员的身份
>辨析：这里设置的签名和登录远程（代码托管中心）的账号和密码没有任何关系
>命令：
``` 差别就是--global全局
#设置系统级别
git config --global user.name rogchen 
git config --global user.email c****@163.com
#查看是否设置成功，路径：当前用户家目录下 ~/.gitconfig
cat ~/.gitconfig
#设置项目级别
git config user.name rogchen 
git config user.email c****@163.com
#查看是否设置成功，路径：当前项目/.git/config
cat .git/config
```
>>项目级别/仓库级别， 仅在当前目录下有效
>>系统用户基本：登录当前 操作系统的用户范围
>>基本优先级
>>>就近原则：项目基本优先于系统用户级别，二者都有采用项目级别
>>>如果只有系统用户级别签名，就以用户级别的签名为准。
>>>二者都没有不允许。
正常使用全局设置，除非需要指明用户
# 正式使用git
## 一、 git本地仓库的操作
```
#查看状态
git status #查看工作区/缓冲区的状态
## on branch master 在主干上
## no commits yet 本地库没有提交文件
## nothing commit *** 没有任何可提交的东西
#添加操作
git add 文件 #将文件添加到缓冲区
#移除操作
git rm --cached 文件名 #从缓冲区将文件移除
#提交操作 可使用 git commit 文件名 #打开vim进行备注体检
#在vim 编辑器使用:set nu 来显示行号
git commit -m 备注 可带文件名（默认提交所有） #提交到本地仓库
# 提交的远程仓库
git push master origin
```

## 二、git版本管理
```
# 查看git提交历史，多屏显示时：空格向下翻页，b向上翻页，q退出
git log #从最后一次提交到第一次提交显示从上往下
## 用漂亮格式显示日志，完整hash值
git log --pretty=oneline
## 用漂亮格式显示日志，更简洁的，只显示部分hash值
git log --onelinx
## 使用指针 需要到那步Head@{3}，移动到当前版本需要3步
git reflog
#1、基于索引值操作-推荐
git reset --hard （部分）索引值 
#2、基于^符号操作 只能往后退配合 --oneline方便
git reset --hard HEAD^ #默认退一步，退两步HEAD^^
#3、基于~符号操作 配合reflog梗简洁
git reset --hard HEAD~n #退n步
```
推荐使用索引来操作，其它需要手动数部署
命令差别 reset命令三个参数 --sort;--mixed;--hard
```
# --soft
## 仅仅在本地库移动HEAD指针(版本差距在缓冲区，本地文件不会变，凸显缓冲区文件)

# --mixed
##在本地库移动HEAD指针，重置缓冲区（本地文件不会变，凸显本地文件）

# --hard
## 在本地库移动HEAD指针，重置缓冲区，重置工作区（重置）

```
常用hard命令来重置

## 三、从删除文件恢复
```
rm 文件 #本地删除
git add 删除的文件
git commit -m 删除文件 文件名
#找回使用
git reset --hard 版本号

# 文件 删除添加到缓冲区未提交到到本地仓库,使用下面命令找回
git reset --hard 指针位置/HEAD #删除已提交到历史位置或当前位置（已提交本地仓库），删除未提交到本地仓库：HEAD
```

### 文件比较-diff
```
# 没有参数就是工作区跟缓冲区比较
git diff 文件名
# 使用指针，本地库中的历史版本。就是工作区和本地库历史记录比较
git diff HEAD^ 文件名
```
不指明文件名比较所有

## 四、分支/冲突
好处：
1、同时并行推进多个功能开发，提高开发效率。
2、各个分支在开发过程中，如果某个分支开发失败，不回对其它分支有影响，失败的分支删除即可。
```
#查看所有分支
git branch -v
# 创建分支
git branch 分支名
# 切换分支
git checkout 分支名
#合并分支 分支线提交到仓库
## a、切换到被合并分支master（也可以其它分支合并到另外分支）
git checkout master
## b、执行merge命令
git merge 新内容分支名
# 解决冲突
## a、合并分支 git merge 文件名
## b、查看冲突文件去编辑它 vim
## c、vim好冲突的内容保存，使用git add 提交修改
## d、使用git commit -m 备注 不能带文件名，
## 善用git status来查看文件状态。
```

## 五、提交到远程仓库
本地仓库创建完成
```
#查看已有远程仓库地址
git remote -v
#将远程地址起别名 origin代表https://**方便记忆
git remote add origin https://github.com/rogchen/test.git
#推送命令
git push origin master
#git push 如果当前分支与多个主机存在追踪关系（存在origin origin1 origin2），那么这个时候-u选项会指定一个默认主机，这样后面就可以不加任何参数使用git push。
git push -u origin master
```
## 六、克隆远程仓库
clone 介绍
> a、完整的把远程库下到本地
> b、创建origin远程地址别名
> c、初始化本地库
```
# 克隆仓库
git clone https://***.git
```

## 七、将其它用户添加到远程仓库管理
去github 选中仓库-collaborators-选择用户。
用户访问邀请地址-接送
这时候用户就可以推送到test这个仓库了。

## 八、拉取pull命令
pull是fetch(拉)和merge(合并)操作 
```
# 抓取远程内容-不该本地工作区内容
git fetch origin master
# 查看我们抓起内容
git checkout origin/master
#这时候使用查看命令就可以看到抓取内容了
cat 文件名
#切回本地库
git checkout master
#远程库合并到本地库
git merge origin/master

#简单使用拉起命令-无法比对
git pull origin master
```
### 使用审核功能  pull request
使用新用户访问test仓库进行fork操作
```
# 克隆项目
git clone 新用户的项目地址.git
# 修改提交-记得使用新用户账号
git push origin master
# 成功提交到新用户的远程仓库
```
进入github管理界面-选择pull requests-选择creat pull requests
>切换到主干用户的github管理界面
选择pull requests-查看新用户提交的pull requests-files changed
进行文件的修改，如果没有问题就进行 merge pull request进行合并代码。

## 九、远程仓库冲突
用户1提交了更新
用户2提交了同个文件更新
这时候报错处于mereging状态
跟本地仓库解决冲突一样。

要点：
>a、如果不是基于github远程库最新版所作的修改，不能推送，必须先拉取。
>b、拉取下来如果进入冲突，则按照“分支冲突解决”操作的方法解决即可。


## 十、使用ssh登录
使用ssh来链接github这样只能使用当前用户,https操作可用切换用户进行多用户操作。
```
# 生成rsa的信息
ssh-keygen -t rsa -C 邮箱地址
#新建ssh的远程地址别名
git remote add origin_ssh git@****仓库地址
## 接下来操作都是用origin_ssh
```




