# Git 快速开始


> Git 是一个免费的开源分布式版本控制系统，旨在快速高效地处理从小型到大型的所有项目。

# 1 快速开始

## 1.1 环境初始化
__安装 Git__

[下载 Windows Setup 并安装（安装时一直Next到安装完成）](https://git-scm.com/download/win)

__初始化本地仓库__
```powershell
git init
```
## 1.2 个人信息配置
__优先级：local > global > system__
- global参数表示配置的是当前主机用户，配置信息保存在用户的家目录下.gitconfig文件中
- local（默认参数）参数表示配置的是当前项目目录，配置信息保存在当前目录.git/config文件中
- system参数表示配置系统所有用户
```powershell
# 查看所有配置信息
git config --list

# 配置当前主机的git用户名和邮件地址
git config --global user.name "xxx"
git config --global user.email xxx@example.com

# 清除配置信息
git config --unset --local user.name
```
## 1.3 创建新仓库
### 基于空目录创建本地仓库
```powershell
git init
```
### 基于已有仓库创建新仓库
可以通过git clone命令直接从远程主机的代码仓库里面完整的复制一份网络上的项目代码。
```powershell
# clone
git clone https://github.com/xxx/xxx.git

# 自定义clone仓库名称
git clone https://github.com/xxx/xxx.git yyy

# 只clone仓库基本信息
git clone https://github.com/xxx/xxx.git yyy --bare
```
## 1.4 git 命令补全
 1. 下载：[git-completion.bash](https://github.com/git/git/blob/master/contrib/completion/git-completion.bash) 
 2. 在 home 目录下创建 .git-completion.bash 文件：mv git-completion.bash ~/.git-completion.bash
 3. 在 /etc/profile.d 目录下创建 gitbash.sh 文件：vim gitbash.sh
 4. 以下内容写入 gitbash.sh：
```powershell
#!/bin/bash
# set git bash
source ~/.git-completion.bash
```
## 1.5 命令查询
```powershell
# 查询常用的命令
git help
# 查询子命令的信息
git help sub_command
# 查询所有的命令
git help -a
git help -g
```
## 1.6 数据结构
### 数据结构
Git有两种数据结构：
- 索引（index/stage/cache）：用于缓存工作目录信息与下一次提交的版本信息，它是可变的。
- 对象数据库（ObjectDB）：仅以追加形式来存储各种信息，它是不变的。包含以下四类对象：
  - blob：它是一个文件的内容。没有适当的文件名、时间戳、或其他元数据。（git add 创建）
  - tree：它包含文件名列表、文件类型、文件和blob或tree对象的引用。tree对象是源树的快照。（git commit 创建）
  - commit：与tree对象组合成历史记录。包含根文件到当前文件的属性信息（名称、时间戳、log等）。（git commit 创建）
  - tag：主要是一个对象间的引用关系，通常它用于追溯的特定版本数据的一个commt对象的数字签名。（git tag 创建）

{{< figure src="pics/代码配置管理-git数据结构.png" width="100%" title="Git 数据结构" >}}

### 数据对象
- blob：
在git仓库所在工作目录中创建一个文件时，使用git add添加文件，其实该动作就是将文件信息和内容压缩成了一个二进制文件(git对象blob)，并将对象保存到本地仓库的对象目录(.git/objects)。
- tree
- commit
- tag：
tag 对象是版本控制管理中的一个非常重要的标志，它和某个指定的 commit 对象有一个关联关系。
```powershell
# 查看仓库基本状况
git status

# 添加到缓存区
git add .

# 查看文件类型
git cat-file -t hashcode

# 查看文件内容
git cat-file -p hashcode

# 根据当前目录结构生成 tree 对象
git write-tree

# 根据元数据和 tree 对象生成 commit 对象
git commit-tree

# 提交变更生成 commit 对象
git commit -m "test"

# 为 commit 对象生成 tag 标签
git tag v0.1
```

## 1.6 区域管理
区域管理是在使用git进行代码版本控制的过程中，实现文件数据移动/保存的几个位置。主要包含以下四个区域：
- 工作目录（working directory）/data/git（不包含.git）
- 暂存区域（staging area）/data/git/.git/index
- 本地仓库（repository-local）/data/git/.git
- 远程仓库（repository-remote）git://path/to/project.git

这四个区域物理上存在于两个地方：
- 当前主机：工作目录、暂存区域、本地仓库，即初始化的git目录
- 远程主机：远程仓库，即远程代码仓库


{{< figure src="pics/代码配置管理-区域管理-区域简介.png" width="100%" title="Git 区域简介" >}}

__区域设置__
- git init 时会自动创建三个区域：工作目录、暂存区域、本地仓库
- git 服务部署时会设置远程仓库

## 1.7 文件状态
git 工具在对文件进行版本管理的过程中，文件主要有以下几种状态：
- untracked（未跟踪）
- unmodified（未修改）
- modified（已修改）
- staged（已暂存）


{{< figure src="pics/代码配置管理-文件状态-变化示意图.png" width="100%" title="Git 文件状态变化示意图" >}}



```powershell
# 查看并删除缓存区的文件
git ls-files --stage
git rm --cache <file>

# 查看日志
git log
```
# 2 git 基础操作
## 2.1 区域操作简介

### 常见命令
- git clone：远程主机 -> 本机主机，包括所有配置信息
	- --bare：只获取仓库信息
- git pull：远程仓库 -> 工作目录，只获取最新代码，并纳入本地仓库管理
- git fetch：远程仓库 -> 工作目录，只获取最新代码，不纳入本地仓库管理
- git push：本地仓库 -> 远程仓库
- git add：工作目录 -> 暂存区
- git commit：暂存区 -> 本地仓库
- git reset：本地仓库 -> 暂存区 or 工作目录
- git checkout：本地仓库 -> 工作目录
- git rm：暂存区 -> 工作目录

## 2.2 获取文件：git clone / pull / fetch
- git clone
  - git clone 支持多种协议，除了 HTTPs 以外，还支持 SSH、Git、本地文件协议等
  - 该命令一般只执行一次
```powershell
cd /data/clone
git clone https://github.com/git/git.git
```
- git fetch
  - 远程仓库 -> 工作目录，只获取最新代码，不纳入本地仓库管理
  - 该命令必须在指定的本地仓库所在的工作目录下执行
```powershell
cd /data/clone/git
git fetch
```
- git pull
  - 远程仓库 -> 工作目录，只获取最新代码，并纳入本地仓库管理
  - 该命令必须在指定的本地仓库所在的工作目录下执行
```powershell
cd /data/clone/git
git pull
```
## 2.3 增加和提交：git add / commit
- git add
  - 工作目录 -> 暂存区
```powershell
# 保存所有的修改
git add -A
# 保存新的添加和修改，但不包括删除（1.x版本），只针对当前目录
git add .
# 保存修改和删除，但不包括新建文件
git add -u
```

- git commit
```powershell
# 提交缓存区的文件 -> 本地仓库
git commit -m "commit"
# 提交所有本地目录已跟踪的文件
git commit -am "commit"
```

- git rebase -i <commit_id>
  - 不建议修改最新的一条信息
```powershell
# 修改提交注释
git commit -m "ccccommit"
git log --graph --oneline
git rebase -i <commit_id> # 使用reword修改注释并提交
```
## 2.4 文件区别：git diff
- git diff
  - 保证提交代码时，文件修改是合规的
```powershell
# 查看工作目录与暂存区文件的区别
git diff
# 查看本地仓库与暂存区文件的区别
git diff --cached
```
## 2.5 删除和移动：git rm / mv
### 删除文件
要从 Git 中移除某个文件，不但要从当前工作目录删除，还要从git版本管理中删除，涉及三个区域：工作目录、暂存区、本地仓库

- git rm
  - __要删除已添加暂存区的文件：先删除暂存区，再删除工作目录__
```powershell
# 删除暂存区的文件
git rm --cached <file>
# 删除工作目录文件
rm -f <file>
```
  - __要删除已提交本地仓库的文件：先删除工作目录，再删除本地仓库，最后提交删除操作__
```powershell
# 删除工作目录文件
rm -f <file>
# 删除本地仓库的文件
git rm <file>
# 提交删除
git commit -m "delete <file>"
```
### 移动或重命名文件
要在 Git 中移动某个文件，但是git并不跟踪文件移动的操作，即使文件重命名，git的元数据也不会显示这是改名操作，但是git会推算出来。涉及到三个区域：工作目录、暂存区、本地仓库。
- git mv
  - __用于移动或重命名文件__

```powershell
# 文件改名并提交
git mv <file_name> <modified_file_name>
git commit -m "modify <file>"

# 移动到新目录
git mv <file> <directory>
git commit -m "move <file> to <directory>"
```
## 2.6 查看提交记录：git log

```powershell
# 查看前 n 条记录
git log -n
# 查看前 n 条的详细记录
git log -n -p
# 查看简要的增改行数统计
git -log --stat -n
# 常用的显示效果
git log --pretty=oneline -n
git log --pretty=short -n
git log --pretty=full -n
git log --pretty=fuller -n
# 自定义显示
git log --pretty=format:"%h - %an, %ar : %s" -n
# 分支场景下常用
git log --pretty=oneline --graph
git log --pretty=oneline --graph --all
# 推荐使用方法
git log --all --oneline --graph
```

## 2.7 文件级别撤销：git reset / checkout / commit
根据撤销对象的状态不同，撤销操作有三种：撤销提交、撤销暂存、撤销修改
### 2.7.1 撤销提交： git commit --amend
撤销上一次提交操作，重新将正确的文件提交到本地仓库，并且两次提交在提交日志中只记录一次修正的提交。
```powershell
# 撤销提交
git commit --amend -m "amend commit"
```
### 2.7.2 撤销暂存：git reset < commit_id|HEAD > < file > 
reset 不但有仓库回滚的效果，还可以指定某个版本的文件进行回滚。

__git commit 提交一个文件，其实就是将 git 的 HEAD 标签移动到指定的文件快照的id，所以 git reset 其实就是将该 HEAD 标签移动到之前标签的动作。__

__git reset 是撤销暂存区数据的一种方式。__

==删除当前暂存区内容==
  - 新文件提交到暂存区
```powershell
git rm --cached <file>
```
  - 文件已提交本地仓库，工作目录对文件进行修改后，提交到暂存区
```powershell
git reset HEAD <file>
```
- 回滚版本库的文件

```powershell
git reset <commit_id> <file>
```
### 2.7.3 撤销修改：git checkout < commit_id|HEAD > -- < file_name >
git checkout <commit_id|HEAD> -- <file_name>

 1. __工作区还原__：如果没有 <commit_id|HEAD>，将暂存区内容覆盖工作目录，add后的文件修改还原
 2. __上次 commit 还原__：< HEAD > 表示将上一次 commit 中的文件覆盖暂存区和工作目录，commit后的文件修改还原 
 3.  __指定 commit 还原__：< commit_id > 表示将指定 commit 中的文件覆盖暂存区和工作目录，commit 后的文件修改还原

## 2.8 对象级别撤销
- reset：将一系列的提交记录取消（不保存历史提交记录）
  - git reset --soft < commit_id >：只将本地仓库回滚到之前的版本，暂存区和工作目录不发生变化
  - git reset --mixed < commit_id >：将本地仓库和暂存区回滚到之前的版本，工作目录不发生变化
  - git reset --hard < commit_id >：将本地仓库、暂存区和工作目录同时回滚到之前的版本

- revert：将指定 commit_id 中的新增文件，在本次提交中删除，对历史的提交记录不受影响。
  - git revert < commit_id >

- reflog + checkout：在找不到历史版本记录的情况下，回滚到之前的版本
  - 方法一：1.使用 reflog 查找所有历史记录，找到 < commit_id >；2.使用 git checkout < commit_id > 切换到对应的版本，并创建分支 git checkout -b temp；3.使用 git checkout master 切换回 master 分支，并使用 git merge temp 合并分支。
  - 方法二：git reset --hard < commit_id> 
 
## 2.9 忽略
git在提交文件时，有三种方法对特殊文件进行过滤：
- 本地仓库 .git/info/exclude
- 指定工作目录 .gitignore
- 项目全局 .gitignore
> .gitignore 来源于 github 开源项目，项目地址：https://github.com/github/gitignore

==忽略优先级：（从高到低）==
 1. 命令级别：git add --ignore-removal
 2. 当前工作目录下的 .gitignore
 3. 上一级工作目录下的 .gitignore
 4. 当前项目目录下的 .git/info/exclude
 5. 全局配置中的忽略规则：git config --global --> ~/.gitconfig

```powershell
# 查看忽略的文件
git ls-files --others
git ls-files --others --exclude-from=.git/info/exclude
```
==忽略规则失效的原因==
- 被忽略的文件已经在暂存区
- 被忽略的文件已经提交到本地仓库

# 3 远程仓库
## 3.1 查看和创建
- 查看远程仓库的基本信息，origin是远程仓库地址的别名
```powershell
git config --list

git remote -v
```
## 3.2 远程仓库部署
### 3.2.1 基本环境
- 远程主机上创建 git 用户，安装 ssh
```powershell
useradd -m git
passwd git
```
- 跨主机免密码认证
```powershell
# 创建密钥
ssh-keygen -t rsa
# 远程主机上传公钥
mkdir /home/git/.ssh
/home/git/.ssh/authorized_keys
vim /etc/ssh/sshd_config
# 重启 ssh 服务
/etc/init.d/ssh restart
```
- 创建远程仓库
```powershell
# 安装 git 工具
mkdir /data/git -p && cd /data/git
git init --bare repo_name.git
```
- 权限配置

```powershell
# 修改仓库属性为 git 用户专用
chown git.git -R repo_name.git
# 修改 /etc/passwd 文件设置 git 用户不允许登陆 shell
vim /etc/passwd
```
## 3.3 远程仓库管理
### 3.3.1 添加远程仓库
```powershell
git remote add <local_name> [repo_url]
```



