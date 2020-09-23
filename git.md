## 概念

1. 工作区

 	2. 暂存区
 	3. 本地仓库
 	4. 远程仓库

## 本地库初始化

命令： `git init`

效果：

注意：.git 目录中存放的是本地库的相关子目录和文件，不要删除也不要随意修改

## 设置签名

形式：

​	用户名：

​	Email地址：

作用：区分不同开发人员的身份

辨析：这里的签名和远程库的账号密码没有任何关系

命令：

​	项目级别/仓库级别：仅在当前本地库范围内有效,保存在当前目录 .git文件中的 config 文件里面

```js
git config user.name XXX
git config user.email aaaa@bbb.com
```

​	系统用户级别：(windows)登录当前操作系统的用户范围 用户目录下 .gitconfig

```js
git config --global user.name XXX
git config --global user.email aaaa@bbb.com
```

​	优先级：就近原则，项目级别优先于系统用户级别

## 基本操作

- 状态查看操作

```js
// 查看工作区 暂存区状态
git status
```

- 添加操作

```js
// 将工作去的 "新建/修改" 添加到暂存区
git add [filename]
```

- 提交操作

```js
// 将暂存区的内容提交到本地库
git commit -m "commit message" [filename]
```

- 查看历史记录操作

```js
git log // 显示全部信息
git log [--pretty=online]|[--online] // 单行显示 --online更简洁
git reflog //
```

- 版本的前进后退：head指针

基于索引值的操作

```js
git reset --hard [索引值]
```

基于 ^ ~ 符号：只能后退

```js
// 回退到前一个版本
git reset --hard HEAD^
// 回退到前两个版本
git reset --hard HEAD^^
// 回退到前 N 个版本
git reset --hard HEAD~N
```

RESET 命令的三个参数

​	--soft：仅仅在本地库移动HEAD指针

​	--mixed：在本地库移动HEAD指针；重置暂存区

​	--hard：在本地库移动HEAD指针；重置暂存区；重置工作区

- 删除文件后找回
  - 前提：删除前，文件存在时的状态提交到了本地库
  - 操作：`git reset --hard [指针位置]`

- 比较文件

```js
// 将工作区和暂存区进行比较
git diff [filename]
// 将工作区和本地库版本进行比较
git diff HEAD [filename]
```

## 分支管理

- 创建分支

```js
git branch [分支名]
```

- 查看分支

```js
git branch -v
```

- 切换分支

```js
git checkout [分支名]
```

- 合并分支
  - 第一步：切换到接受修改的分支上（被合并的分支）
  - 第二步：执行合并操作

```js
git merge [修改后的分支名]
```

- 解决冲突

1. 修改冲突文件
2. `git add [conflit filename]`
3. `git commit -m "conmmit message"`(不能带有文件名)

## 本地库和远程库的交互

- 本地仓库为远程仓库创建别名

```js
// 查看当前远程库的状态
git remote -v

// 为远程仓库添加别名,别名为 origin
git remote add [origin] [ssh|https]
```

- 推送

```js
git push [origin] [branchname]
```

- 克隆

```js
git clone [ssh|https]
```

1. 把远程库下载到本地
2. 创建origin远程地址别名
3. 初始化本地库

- 拉取：fetch+merge === pull

```js
// 拉取远程仓库到 origin master
git fetch [origin] [master]

// 将origin master仓库的内容合并到本地仓库的master
git merger [origin/master]

// 等价于上面两条
git pull origin master
```

- 协同开发时的冲突的解决

如果不是基于git仓库最新版所做的修改，不能推送，必须先拉取