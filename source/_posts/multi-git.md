---
title: Mac下配置多个Git账户
date: 2019-07-08 21:30:47
top:
categories:
- 学习笔记
tags:
- git
---

默认情况下，我们使用 `git` 都是配置一个全局 `user.name` 和 `user.email` ，每次要用时使用该全局参数即可。

但有时候，我们可能要同时使用多个不同的远程仓库，比如个人用的 `github` 使用 `邮箱a` ，公司用的 `gitlab/gitea` 使用 `邮箱b` ，等等。

那么，如何在本地的Mac上配置多个git账户呢。

我们继续往下看 ～

<!--more-->

![](/images/mg-1.jpg)

### 需求

#### 起因

起因很简单，就是因为自己一直用 `github` ，然后公司现在用 `gitea`  。

然后，邮箱还不一样，也就是 `生成的密钥` 是不一样的，所以要配置多个 `git` 账号在一台电脑上。

然后，就有了本文....

#### 相关细节

关于 **一台电脑使用多种类型远程仓库(`github`、`gitlab`、`gitea` 等)是否需要配多个git** ，这里有几个细节需要说明：

- 邮箱决定生成的密钥key是否一致；
- `ssh` 连接才需要密钥(配置)，`http` 连接不需要密钥/邮箱(配置)；
- 对于未配置 `user.name` 和 `user.email` 的 `git` 账号，`git` 提交记录里默认使用当前电脑 `登陆的用户名` 和 `电脑名` ；

**所以：**

- 如果使用 `http` 类型来 `pull`、`push` 代码，**不需要配置！！** 
- 如果多种类型的远程仓库，使用的是同一个邮箱，**也不需要配置！！**
- 如果需要配置的话，**需要配置每个项目的 `user.name` 和 `user.email`** ，而 **不能配置全局的 `user.name` 和 `user.email`** 。

### 配置步骤

#### 清除全局的 `user.name` 和 `user.email`

一般情况下，可能之前已使用了以下脚本进行了全局配置：

```bash
git config --global user.name "xxx"	// 配置全局用户名，如github上注册的用户名
git config --global user.email "yyy"	// 配置全局邮箱，如github上注册的邮箱
```

如果不确定是否已经全局配置，可使用下面的命令进行查看：

```bash
git config --global user.name
git config --global user.email
```

如果已经配置了，先删掉全局配置

```bash
git config --global --unset user.name
git config --global --unset user.email
```



#### 为每个账号生成一对密钥

首先进入保存密钥的目录：

```bash
cd ~/.ssh	// 进入目录，该目录下保存生成的密钥
```

然后，根据 `账号邮箱` 生成 `密钥` 。

```bash
ssh-keygen -t rsa -C "xxx@qq.com"
```

输入完成后，会有如下提示：

```bash
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/qiu/.ssh/id_rsa):
```

这里要求对密钥进行命名。

- 默认的文件名是 `id_rsa` 。
- 为了方便区分，我们可以命名为 `id_rsa_github` 和 `id_rsa_gitea` ，接下来一直回车，直到密钥生成。
- 每次生成的密钥都是成对的，比如 `id_rsa_github` 和 `id_rsa_github.pub` 。其中 `id_rsa_github` 是私钥，是保存在 `本地` 用来验证服务器的，不能泄露的；`id_rsa_github.pub` 是公钥，是添加到 `托管网站` 上的。
- ![](/images/mg-2.jpg)

#### 私钥添加到本地

`SSH` 协议的原理，就是在托管网站上使用 `公钥` ，在本地使用 `私钥` 。这样本地仓库就可以和远程仓库进行通信了。

上一步已经生成了 `密钥文件` ，接下来需要使用 `密钥文件` 。首先是在本地使用 `密钥文件` ：

```bash
ssh-add ~/.ssh/id_rsa_github	// 将github私钥添加到本地
ssh-add ~/.ssh/id_rsa_gitea	// 将gitea私钥添加到本地
```

为了校验本地是否添加成功，可以使用 `ssh-add -l` 命令进行查看

#### 对本地密钥进行配置

由于添加了多个 `密钥文件` ，所以需要对这多个密钥进行管理。在 `.ssh` 目录下新建一个 `config` 文件：

```bash
touch config
```

文件中的内容如下：

```
Host github.com // 网站的别名，随意取
HostName github.com // 托管网站的域名
User xxx // 托管网站上的用户名
IdentityFile ~/.ssh/id_rsa_github // 使用的密钥文件
Port zz	// 可选，端口

// Gitea的配置相同
Host git.ym
HostName git.ym
User yyy
IdentityFile ~/.ssh/id_rsa_gitea
```

**注意：**

- `config` 文件中不允许有任何注释，所以 `//` 注释**请删掉**！
- `Host` 别名是用来替代 `HostName` 域名的，所以为了使原来的配置继续生效，建议 **`Host` 设置成和 `HostName` 一样** 。

#### 公钥添加到托管网站上

以 `github` 为例

- 先在本地复制公钥。进入 `.ssh` 目录，复制 `id_rsa_github.pub` 里的公钥。
- 然后登陆 `github`，添加复制的公钥。
- ![](/images/mg-3.jpg)

### 如何使用

#### 如果是用远程仓库clone下来

**使用 `SSH` 协议**

~~也不用任何操作？(不确定，待跟进..)~~

**使用 `HTTP` 协议**

不需要任何操作，`git clone` 的时候会要求你输入 `user.name` 和 `user.email` 。

#### 如果是本地已有的仓库

需要配置每个仓库的 `user.name` 和 `user.email` ：

进入到对应仓库的目录下。

执行以下命令：

```bash
git config user.name "xxx"
git config user.email "yyy@qq.com"
```

OK！大功告成 ！