---
title: 关于 hexo 的一次踩坑之旅
date: 2019-05-14 19:46:17
categories: 
- 学习笔记
tags: 
- hexo
- web前端

---

## 关于 hexo 的一次踩坑之旅。

之前一直是在自己搭的网站写博客，markdown写好后用编辑器转成 html 格式，然后直接扔上去。emmmmm… 巨丑。

后来决定弄个漂亮点的博客，研究了下，决定用 hexo + github 来部署。看完文档感觉挺简单，结果花了几天时间才弄好，中间也碰到几个坑，记录一下。

<!--more-->

### npm 权限问题

因为之前安装 node 的时候自动安装了 npm ， 然后 Mac 下 npm 是默认安装在 `/usr/local/` 路径下的，而该路径下很多目录和文件时 **只读的** ，然后在安装 hexo 的时候，会报权限错误；

```bash
// 此处会报权限错误
npm install -g hexo-cli
```

#### 解决方法

网上找了一下，主要有四种解决方法：

#### 方法一

为当前用户添加 `npm安装目录` 下所有文件的写权限。

```bash
sudo chown -R (whoami)(npm config get prefix)/{lib/node_modules,bin,share}
```

**注意** ：如果路径是 `/usr/local/` 的话不推荐这种方式，因为改权限会弄乱系统的权限，以后可能导致奇怪的错误(比如无法跟当前系统的其他用户共享)。

#### 方法二

把 **npm** 卸载，并安装在一个有读写权限的路径下；

推荐 `~/.npm-global` 路径下；

1.新建一个全局安装的路径；

```bash
mkdir ~/.npm-global
```

2.配置 npm 使用新的路径；

```bash
npm config set prefix '~/.npm-global'
```

3.打开或者新建 `~/.profile` ，加入下面一行；

```bash
export PATH=~/.npm-global/bin:$PATH
```

4.重新启动终端；

```bash
source ~/.profile
```

5.测试：安装一个全局包试试

```bash
npm install -g jslint

[luncher@localhost aaa]$ ls ~/.npm-global/bin/
jshint
[luncher@localhost aaa]$ 
```

#### 方法三(推荐)

使用 **nvm** 。nvm 可以在一台电脑上管理不同版本的 node，并且使用 nvm 安装 npm 后，npm 默认的安装路径是有读写权限的，也就是说，以后 npm 安装任何包都不用再使用 `sudo` 参数了。

官网的解决方案：[<https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally>](https://docs.npmjs.com/resolving-eacces-permissions-errors-when-installing-packages-globally)；

这里有个细节要注意：如果你本地已经安装了npm，你得先把本地的npm卸载，再安装nvm，再使用nvm安装npm；

**1.卸载已安装到全局的 node/npm**

node 命令在 `/usr/local/bin/node`，npm 命令在 `/usr/local/lib/node_modules/npm` 下；

查看已经安装的全局模块，以便卸载后再按不同的 node 版本重新安装；

```bash
npm ls -g --depth=0
```

```bash
sudo rm -rf /usr/local/lib/node_modules	#删除全局 node_modules 目录
sudo rm /usr/local/bin/node	#删除 node
cd  /usr/local/bin && ls -l | grep "../lib/node_modules/" | awk '{print $9}'| xargs rm #删除全局 node 模块注册的软链
```

**2.安装 nvm**

```bash
curl -o- https://raw.githubusercontent.com/creationix/nvm/v0.32.1/install.sh | bash
```

#### 方法四

借助第三方工具安装 node

- mac 系统借助 **brew** 安装 `node`

```bash
brew install node
```

- centos 系统借助 **yum** 安装 `node`

```bash
yum install node
```

### 部署分支问题

由于 hexo 部署(`hexo d`) 的原理是，把站点上的所有静态文件删除，并把本地待部署的全部静态站点文件(/public/目录下) push 到远程库上，即完全覆盖；所以 hexo 官方文档有这样一句话 **建议使用 master 作为写作分支，新建一个 public 分支作为发布分支**。在按照这样创建分支后，发现网站打不开，然后在 [github pages](https://help.github.com/en/articles/configuring-a-publishing-source-for-github-pages) 的文档里看到这样几句话：

- Reponsitory 必须以 username.github.io 命令，这个 username 为 github 的账户名，网站部署成功后，使用 http://username.github.io 进行访问；
- 必须使用 **master** 分支管理网站静态文件。注意这句话，必须使用 **master** 分支。emmmmm….. 就是这么坑。

所以，github的建议是：

使用 **master** 作为发布分支，使用 **hexo** 作为写作分支。

### Hexo 的使用

Hexo 的使用流程一般如下：

[官方文档](https://hexo.io/zh-cn/docs/index.html)

1.安装 hexo

```bash
npm install -g hexo-cli
```

2.创建 项目

```bash
hexo init <folder>
cd <folder>
npm install
```

3.新建 文章

```bash
hexo new [layout] <title>
```

4.生成静态文件

```bash
hexo g
// or
hexo generate
```

5.本地预览

```bash
hexo s
// or 
hexo server
```

6.部署到服务器

```bash
hexo d
// or 
hexo deploy
```

### 关于主题

主题的使用很简单，以下几个步骤即可：

1.clone 主题到本地

```bash
cd your_blog_dir
git clone https://github.com/DIYgod/hexo-theme-sagiri.git themes/sagiri
```

2.修改 hexo 配置文件使用主题

```js
## Themes: https://hexo.io/themes/
theme: sagiri
```

3.修改 主题配置文件，进行个性化设置

```bash
// 路径为：your_blog_dir/themes/sagiri/_config.yml
```

### 推荐主题

搜集了一些比较好看的主题：

[**sagiri**](https://github.com/DIYgod/hexo-theme-sagiri) ：[https://diygod.me](https://diygod.me/)

[**Material X**](https://xaoxuu.com/wiki/material-x/) ：[https://xaoxuu.com](https://xaoxuu.com)

**[Inside](https://github.com/elmorec/hexo-theme-inside)** ：[https://blog.oniuo.com](https://blog.oniuo.com)

**[Next](http://theme-next.iissnan.com/)** ：[https://blog.tolvyou.cn](https://blog.tolvyou.cn)

### 旧版博客

最后，就是巨丑的[旧版博客](http://www.jianwill.cn)了…….