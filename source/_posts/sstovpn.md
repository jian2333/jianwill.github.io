---
title: ss + proxifier 实现vpn游戏代理
date: 2019-09-10 22:40:54
top:
categories:
- 一起哈啤
tags:
- ss
- vpn
---

之前搭过一个 `ss` (搭建教程可以参考 [自建ss/ssr服务器教程](https://github.com/Alvin9999/new-pac/wiki/%E8%87%AA%E5%BB%BAss%E6%9C%8D%E5%8A%A1%E5%99%A8%E6%95%99%E7%A8%8B))，但  `ss` 只能在浏览器上用，如果想像 `vpn` 一样代理游戏的话，该怎么办呢？

好在这个问题已经有解决方案了，就是搭配 `Proxifier` 使用。

下面，就是设置 `Proxifier` 的步骤。

<!--more-->

### 下载 `ss` 和 `Proxifier` 

**下载 `ss`**

- **Windows SSR客户端** ：[下载地址](https://github.com/shadowsocksr-backup/shadowsocksr-csharp/releases)
- **Mac SSR客户端** ：[下载地址](https://github.com/shadowsocksr-backup/ShadowsocksX-NG/releases)

**下载 `Proxifier`**

- **Windows 便携版** ：[官网地址](https://www.proxifier.com/distr/ProxifierPE.zip)
- **Windows 安装版** ：[官网地址](https://www.proxifier.com/distr/ProxifierSetup.exe)
- **Mac版** ：[官网地址](https://www.proxifier.com/distr/ProxifierMac.zip)

### 实现步骤

#### 启动ss

启动并设置好 `ss` ，保证能正常运行即可。

#### 启动并设置 `Proxifier`

启动 `Proxifier` 后，要配置以下三个步骤：

- 代理服务器设置
- 代理规则设置
- 域名解析设置

##### 代理服务器设置

- `profile` --> `Proxy Servers`
- 添加 `Server` 
- 设置 `Address` 和 `Port` 
- 修改 `Protocol`
- `Check`
- `OK`

![](/images/sspro-1.jpg)

![](/images/sspro-2.jpg)

##### 代理规则设置

- `profile` --> `Proxification Rules`
- 设置 `Localhost`
- 设置 `需要代理的游戏`，比如 `GTAV`
- 设置 `默认连接方式`

![](/images/sspro-3.jpg)

![](/images/sspro-4.jpg)

![](/images/sspro-5.jpg)

##### 域名解析设置

- `profile` --> `Name Resolution`
- 设置 `通过代理服务器解析域名`
- 设置 `不解析以下主机`

![](/images/sspro-6.jpg)

### 效果图

下面是 `ss` + `Proxifier` 代理后，启动 `GTAV` 的效果图

![](/images/sspro-7.jpg)

然后，终于可以愉快的体验 **GTAV 赌场DLC** 了...

(手动狗头，滑鸡!)

