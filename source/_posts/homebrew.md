---
title: homebrew安装低版本应用
date: 2024-07-05 14:19:13
top:
categories:
- 学习笔记
tags:
- mac
---

Mac系统下，我们一般用 `homebrew` 安装应用，安装的应用默认是当前 `homebrew` 版本内置的该应用最高版本，那如何安装该应用的低版本呢。

### 背景

Mac 下使用 `homebrew` 安装应用，一般我们会关闭 `homebrew` 的自动更新以提高安装效率。

当我们使用几年后，但电脑系统和 `homebrew`没更新后，我们会发现当前 `homebrew` 版本的应用都比较低。比如现在 `2024-07-05`, 我用的 `homebrew` 还是2020年的，执行 `brew install nginx` 后安装的 `nginx` 就是2020年左右的，大概是 `1.19.0` 左右。

于是，我们更新 `homebrew` 为最新版本，并希望安装最新版本的 `nginx`，于是执行 `homebrew update` ，成功后执行 `brew install nginx`，然后，问题就出现了。安装 `nginx` 失败，提示：`您当前的osx 系统版本(和xcode)版本过低，最新版的nginx(1.25.1)不支持当前版本，安装失败！`。

如果不想升级 `osx` 系统，那如何用高版本的 `homebrew` 安装任意版本的 `应用` 呢？

<!--more-->

### 方案

在多次尝试后总结出来的经验：

#### 1、更新 `homebrew` 为最新版本

```shell
brew update
```

#### 2、查询想要安装的应用及版本。

1. 去 `homebrew` 官方目录下找到该应用，地址为 [github-formula](https://github.com/Homebrew/homebrew-core/tree/master/Formula)；
2. 点开具体的 `xxx.rb` 文件，点击右上角 `History`，通过 `git` 提交记录，找到想安装的版本，点击右侧第二个图标(View code at this point)；
3. 点击 `Copy raw file`，复制这版本的配置文件。

#### 3、修改当前 `homebrew`的 `formula` 文件为指定版本的配置文件

- ```shell
  # brew edit <packageName>
  brew edit nginx
  ```

- 粘贴剪切板内容，替换打开的文件，保存关闭。

#### 4、开启使用手动`formula`配置。(重要！！！网上所有的方案都缺少这一步，所以都不生效)

- 默认情况下，使用 `brew install <package>`，会自动更新 `formula/xxx.rb` 配置文件，再执行安装。也就是 第3步 修改的配置文件并不会生效。

- 需要做一些设置，让配置文件生效，这样执行 `brew install <package>`，就能使用修改的配置文件，即 **可以安装任意版本的应用**。

  - 方法一：只当前终端生效：

    ```shell
    HOMEBREW_NO_INSTALL_FROM_API=1 && brew install <package>
    ```

  - 方法二：设置到终端配置文件，在终端永久生效：

    ```shell
    # 新增相关配置到配置文件最后
    echo "export HOMEBREW_NO_INSTALL_FROM_API=1" >> ~/.zshrc
    
    # 重启终端
    source ~/.zshrc
    ```

#### 5、安装应用

```shell
brew install nginx
```

PS：低版本 `osx` + 高版本 `homebrew` 下，安装的大部分软件都会失败，提示不支持当前 `osx` 系统，那哪个版本的 `<package>` 支持当前 `osx` 系统，得去 `homebrew-core-formula` git记录里上，或者一个个尝试。

### 实操

实操以上面安装 `nginx` 为🌰：

**1、更新 `homebrew`**

```shell
brew update
```

**2、查询想要安装的应用及版本**

![](/images/hb-1.jpg)

![](/images/hb-2.jpg)

![](/images/hb-3.jpg)

![](/images/hb-4.jpg)

**3、修改当前 `homebrew`的 `formula` 文件为指定版本的配置文件**

```shell
brew edit nginx
```

![](/images/hb-5.jpg)

**4、开启使用手动`formula`配置。**

![](/images/hb-6.jpg)

**5、安装应用**

```shell
brew install nginx
```

安装完之后可以使用 `nginx -v` 查看版本。



### 常用命令

**安装 `homebrew`**

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

**卸载 `homebrew`**

```shell
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/uninstall.sh)"
```

**更新 `homebrew`**

```shell
brew update
```

**安装应用**

```shell
brew install <package>
```

**更新应用**

```shell
brew upgrade <package>
```

**卸载应用**

```shell
brew uninstall <package>
```

**查看 `homebrew` 版本**

```shell
brew --version
brew -v
```

**列出所有已安装的应用**

```shell
brew list
```

**查询远程残酷的应用，已安装的会显示绿色的勾**

```shell
brew search <package>
brew search /<package的正则表达式>/
```

**查询某个应用信息（及版本号，来源 `formula/xxx.rb`）**

```shell
brew info <package>
```

**修改某个应用的 `formula` 配置文件**

```shell
brew edit <package>
```

