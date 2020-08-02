# GitHub

## 注册

[gitHub地址](https://github.com/)

注册完后进入邮箱验证

## 1.创建仓库

![](GitHub.assets/1589008351(1).png)

仓库主页

![](GitHub.assets/1589009090(1).png)

## 2.下载git

### 2.1下载地址

[下载地址](https://git-scm.com/downloads)

### 2.2初始化git基本信息

#### 2.2.1用git命令行输入以上两条命令

```
1.设置用户名
git config --global user.name 'guoqisheng2018'
2.设置用户名邮箱
git config --global user.email '839774432@qq.com'
```

#### 2.2.2初始化本地仓库

在文件内输入命令初始化本地仓库

```
git init
```

### 2.3git命令

工作区域提交到本地暂存区域

```
添加全部文件
git add .
添加单个文件
git add 文件名
```

本地暂存区域提交到本地git仓库

```
git commit -m'提交描述'
```

从远程仓库克隆到本地仓库

![](GitHub.assets/1589035895(1).png)

```
git clone 'https://github.com/guoqisheng2018/web-spring-boot.git'
```

从远程仓库拉取到本地仓库

```
git pull
```

从本地仓库提交

```
git push
```

### 2.4git第一次提交到远程仓库（建立和远程仓库联系）

```
git与远程仓库建立连接
git remote add origin https://github.com/guoqisheng2018/web-spring-boot.git
将项目推送到远程仓库
git push -u origin master 
如果提交不上使用命令查看远程仓库信息
git remote -v
删除错误仓库
git remote remove 仓库名
再次将项目推送至远程仓库，如若还推送不上
git pull --rebase origin master
如若强拉失败
git pull origin master --allow-unrelated-histories
再次推送至远程仓库
```

## 复刻使用方法

[复刻使用方法](https://docs.github.com/cn/github/collaborating-with-issues-and-pull-requests/working-with-forks)

```
git remote -v
http://122.152.201.59/guoqisheng/century21-server.git
git checkout master
git merge upstream/master
git pull http://122.152.201.59/century21/century21-server.git




```

