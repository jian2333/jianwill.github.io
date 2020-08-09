---
title: 一种新的科学上网方式 -- v2ray
date: 2020-01-05 18:23:55
top:
categories:
- 一起哈啤
tags:
- v2ray
- vpn
- 科学上网
---

由于某些众所周知的原因，emmmmm....，不久前的血色十月，一大波梯子被封了。毫无意外，我的 `ss` 也被封了，而且从那之后，`ss` 就变得不好用了，速度慢，还经常上不了网。

然后吧，研究了下目前比较主流的方式 `v2ray` ，包括 `搭建方式` 、`速度优化`、`ip伪装防封` 等，发现 `v2ray : ws + tls + nginx + cdn` 的方式速度和伪装性都很好。

再然后吧，今天就总结一下这个好了.....

<!--more-->

本文主要总结了以下几个方面：

- [最终总结及推荐](#最终总结及推荐)
- [名词解释](#名词解释)
- [具体搭建流程](#具体搭建流程)
- [效果](#效果)
- [相关资料介绍](#相关资料介绍)

### 最终总结及推荐

根据实际测试下来的结果，最终推荐 **google云香港服务器+ws+tls+nginx+伪装主页+bbr** 这种配置。不推荐使用 `cdn` 。因为免费的 `cdn` 虽然能隐藏真实`ip`，但是会 **大大降低网络速度**，再加上 `ws+tls+nginx+伪装主页`，已经能达到很好的欺骗 `GFW` 的效果了，而收费的 `cdn` 虽然效果好，但是**太贵了**！所以不推荐使用 `cdn`。

#### 服务器

- 速度最快的供应商是 [`google云`](<https://cloud.google.com/>)，其次是 [`搬瓦工`](https://bandwagonhost.com/)，最后是 [`vultr`](https://www.vultr.com/)。
- 香港服务器最快(`vultr`没有香港服务器)。
- `搬瓦工` 服务器速度：香港服务器 > CN2 GIA-E = CN2 GIA > KVM = vultr 。

#### 协议

- `mkcp` 协议搭建简单，但是速度不够快，伪装还行，但在有些场合连接不稳定(比如 **在公司网络 ，我就经常连不上.....** )。
- `ws+tls` 协议搭建复杂一下，需要有域名，但是速度快，而且稳定，**在公司网络，一样稳如老狗！！**
- `ws+tls+nginx+cdn` 伪装性更好，但是更复杂，需要懂一些`nginx`的相关配置，还要会`cdn`代理伪装。不过不推荐 `cdn` 代理。原因之前说到了：
  - 免费的 **大大降低网速**，收费的太贵。(免费的`cloudflare`服务器在美国，`ping` 变成200+， 直连`google云香港服务器`  `ping` 才20+，速度差10倍)。
  - `ws+tls+nginx+主页伪装` 已经能很好的伪装了，能 **很好的骗过 `GFW` 了** 。
- 所以推荐的是 `ws+tls+nginx+伪装主页`。

#### bbr加速

- `bbr` 是 `google` 的一种算法加速，对速度 **影响极大**，**强烈推荐开启**。
- 就个人测试而言，同样配置 `youtube 4K 60fps` 下，未开启 `bbr` 速度为 `20k Kbps`，开启 `bbr` 后为 `150k Kbps`，**速度相差极大**。
- ~~直接使用一键安装脚本里的命令开启即可，选择 `bbrPlus版本`，即 `11-2-7`~~ (2020.08.08更新)一键脚本的`bbr安装启动`命令已经失效，请使用下方新命令。

### 名词解释

**一键安装脚本**

一键安装 `v2ray+ws+tls+nginx+https证书+https证书定时续签`。

```bash
bash <(curl -L -s https://raw.githubusercontent.com/wulabing/V2Ray_ws-tls_bash_onekey/master/install.sh) | tee v2ray_ins.log
```

相关路径：

- Web 目录：`/home/wwwroot/3DCEList`
- V2ray 服务端配置：`/etc/v2ray/config.json`
- V2ray 客户端配置: `~/v2ray_info.txt`
- Nginx 目录： `/etc/nginx`
- 证书文件:  `/data/v2ray.key 和 /data/v2ray.crt`

**cdn**

用来 `伪装真实ip`，还有防 `DDOS流量攻击` 等其他功能。

**cloudflare**

一个**免费**配置 `cdn` 的[网站](<https://www.cloudflare.com/>)，但是不推荐使用，原因前面有说到，会 **大大降低网络速度**。

**ws**

`websocket` ，一种协议。需要有域名。把翻墙流量伪装成正常访问配置域名的流量。

**tls**

一种加密协议，需要使用域名。一般配合 `ws` 使用。

**nginx**(非必须)

服务器配置 `web网站` 使用，配置一个 `.html` 文件 `伪装成静态网站`。

**https证书**(非必须)

给伪装的 `web` 网站添加证书，让网站更真实。证书有效期3个月，`一键安装脚本` 会自动 `申请，下载证书`，并添加 `证书自动续签任务`，**但是，需要自己修改nginx配置，选择证书路径！！**

**bbr一键安装脚本**

```bash
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"
chmod +x tcp.sh
./tcp.sh
```



### 具体搭建流程

#### 申请域名

可以在 [freenom](https://www.freenom.com/) 申请免费的域名。不过这个网站注册比较麻烦.....

也可以在 [阿里云](https://cn.aliyun.com/)，[腾讯云](https://cloud.tencent.com/) 等注册，都可以。

#### 配置cdn

免费的话可以在 [cloudflare](https://www.cloudflare.com/) 配置 cdn。

- 配置时，代理 `Proxy status` 记得点成灰色，在 `bbr` 安装完成后，在决定点亮。
- 点亮不点亮都可以使用，点亮即使用代理，伪装了真实ip，但速度会慢很多，不点亮即直连。
- 配置好后，需要在 `域名管理` 的地方(比例如`阿里云->域名->管理->cdn`)，将 `dns` 的2个地址添加上去。

#### 创建vm实例

使用主机供应商添加 `vm实例`即可，推荐 `google云`， 速度最快。

以下以 `google` 为标准：

创建 `vm` 实例

- 选择 `香港+N1+f1-micro+勾选http、https+网络标记rz,cz`
- 编辑防火墙规则 `rz` ：`方向：入站；目标：网络中的实例；来源过滤条件：IP地址范围；来源IP地址范围：0.0.0.0/0`
- 编辑防火墙规则 `cz`：`方向：出站；目标：网络中的实例；来源过滤条件：IP地址范围；来源IP地址范围：0.0.0.0/0`

#### 一键脚本安装

在 `google云 vm实例` 页面，点击 `SSH` 链接：

- 输入 `sudo -i` 获取 `root` 权限；

- 输入一键安装脚本

  ```bash
  bash <(curl -L -s https://raw.githubusercontent.com/wulabing/V2Ray_ws-tls_bash_onekey/master/install.sh) | tee v2ray_ins.log
  ```

- 按要求输入域名，等待脚本执行完成即可。

#### 修改nginx配置文件(非必须)

**添加 `nginx` 命令到环境变量**

```bash
echo PATH=$PATH:/etc/nginx/sbin >> /etc/profile
source /etc/profile
```

**下载并修改nginx配置文件**

窗口右上角下载，下载后编辑，主要修改 `域名、端口号、/ptah、https证书路径`

可以参考下面的配置

该配置做了以下几件事：

- 重定向 `http(80端口)` 到 `https(443端口)`。
- 重定向 `/path` 或 `websocket` 协议到 `23188` 端口，`v2ray ws` 协议用。
- 修改 `https证书路径`。
- 修改 `域名、端口号`。

```nginx

user  root;
worker_processes  3;

#error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

#pid        logs/nginx.pid;


events {
    worker_connections  4096;
}


http {
    include       mime.types;
    default_type  application/octet-stream;

    #log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
    #                  '$status $body_bytes_sent "$http_referer" '
    #                  '"$http_user_agent" "$http_x_forwarded_for"';

    #access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    #gzip  on;

    server {
        listen       80;
        server_name  kxsw.jianwill.cn;
        return 301 https://kxsw.jianwill.cn:443;

        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root   /home/wwwroot/3DCEList;
            index  index.html index.htm;
        }

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    server {
        listen       443 ssl http2;
        server_name  kxsw.jianwill.cn;

        ssl_certificate      /data/v2ray.crt;
        ssl_certificate_key  /data/v2ray.key;

        ssl_session_cache    shared:SSL:1m;
        ssl_session_timeout  5m;

        ssl_ciphers  HIGH:!aNULL:!MD5;
        ssl_prefer_server_ciphers  on;
        ssl_protocols TLSv1 TLSv1.1 TLSv1.2;

        location / {
            root /home/wwwroot/3DCEList;
            index index.html index.htm;
        }

        location /8fded3d7/ {
            proxy_redirect off;
            proxy_pass http://127.0.0.1:23188;
            proxy_http_version 1.1;
            proxy_set_header Upgrade $http_upgrade;
            proxy_set_header Connection "upgrade";
            proxy_set_header Host $http_host;
            proxy_intercept_errors on;
            if ($http_upgrade = "websocket" ){
               proxy_pass http://127.0.0.1:23188;
            }
        }
    }

include conf.d/*.conf;
}
```

**上传nginx.conf配置文件**
窗口右上角，上传文件，上传后到文件默认在 `/home/google用户名/` 路径下，比如我的是 `/home/jian23333/`

**拷贝nginx.conf到nginx配置目录**

`nginx` 配置文件路径是 `/etc/nginx/conf/`

所以，执行下面的命令

```bash
cd /home/jian23333/
cp nginx.conf /etc/nginx/conf/
// 默认会直接覆盖
```

PS： 如果上传的时候，已经存在 `nginx.conf`，上传的文件会被命名为 `nginx_(n).conf`，后面执行命令的时候 `(` 这个符号可能会识别报错，这时可以删掉原来的所有 `nginx_(n).conf` 文件，重新上传，就命名成 `nginx.conf` 文件了。

删除命令

```bash
cd /home/jian23333/
rm *.conf
```

**校验并重新启动nginx**

- 校验：`nginx -t`


- 重新启动：`nginx -s reload`


- 查看nginx是否启动：`ps -ef | grep nginx`，看是否有 `master` 的行


如果校验报错，根据错误提示，修改配置文件。

可以使用 `vi /etc/nginx/conf/nginx.conf` 直接修改，也可以使用上面的方式下载到本地，修改，再上传的方式。

如果提示 `nginx: command not found` ，就是没有把 `nginx` 配置到环境变量中，重新执行上面的 `第一步：添加nginx命令到环境变量` 即可。

#### 安装bbr加速

**安装bbr**

执行bbr一键安装脚本

```bash
wget -N --no-check-certificate "https://raw.githubusercontent.com/chiakge/Linux-NetSpeed/master/tcp.sh"
chmod +x tcp.sh
./tcp.sh
```

依次执行`2-7`，即`安装bbrPlus`，`使用bbrPlus加速`

安装之后使用`./tcp.sh`即可重复执行命令

~~期间会重启一次。~~

经测试，发现 `bbrPlus` 比 `bbr` 加速效果更好。

**检查bbr是否安装成功**

- 验证当前`TCP`的控制算法(**是否安装`bbr`**)：

  ```bash
  sysctl net.ipv4.tcp_available_congestion_control
  ```

  返回值一般为：

  ```bash
  net.ipv4.tcp_available_congestion_control = bbr cubic reno
  // 或者
  net.ipv4.tcp_available_congestion_control = reno cubic bbr
  ```

- 验证`BBR`是否已经启动(**是否启动`bbr`**)：

  ```bash
  sysctl net.ipv4.tcp_congestion_control
  ```

  返回值一般为：

  ```bash
  net.ipv4.tcp_congestion_control = bbr
  ```

- 或者：

  ```bash
  lsmod | grep bbr
  ```

  返回值一般为：

  ```bash
  // 返回值有tcp_bbrplus 即说明bbr已经启动。
  // PS：但不是所有vps都会有此返回值，没有也正常。
  tcp_bbrplus            20480  21
  ```

- **注意：** `bbrPlus` 有时候会自动失效(比如重启服务器后？)，此时需手动再次开启`bbrPlus`

#### 其他问题

**如果要更换域名，发现域名证书没有一起更新怎么办？**

~~删掉证书文件，重新执行一键安装脚本，选择重新安装 `ws+tls+nginx`~~

```bash
// 作废！作废！作废！
cd /data/
rm v2ray.crt
rm v2ray.key
bash <(curl -L -s https://raw.githubusercontent.com/wulabing/V2Ray_ws-tls_bash_onekey/master/install.sh) | tee v2ray_ins.log
1
```

一键安装脚本已更新并添加**删除证书遗留文件**功能，执行一键安装脚本，选择`16`即可

```bash
bash <(curl -L -s https://raw.githubusercontent.com/wulabing/V2Ray_ws-tls_bash_onekey/master/install.sh) | tee v2ray_ins.log
16
```

**如何重启服务器**

```bash
reboot
```



### 效果

`bbr` 加速后，`youtube 4K 60fps` ，速度可以达到 `170k Kbps`，网速 `17m/s` （我用的100M电信，稳定20k Kbps就可以流畅看 4K 60fps）。

所以，现在是 **秒开4K，8K**

**用户体检极好！...**

![](/images/v2ray-1.jpg)

**而且还有伪装主页**(https加密)

地址为：[kxsw.jianwill.cn](https://kxsw.jianwill.cn/)

### 相关资料介绍

折腾了几个月，尝试了多家供应商不同地区的服务器，`vultr 美国、巴黎、新加坡、日本等`，`google云 香港等`，不同的协议 `tcp、mckp伪装BT下载、mkcp伪装Facetime童话、mkcp伪装微信视频、websocket`，`cdn`，`nginx` 等多种方案，最后决定使用 `google云香港服务器+ws+tls+nginx+伪装主页` 的方式。

参考了很多资料，视频，下面为几个主要的。

1. [视频:v2ray+ws+tls+nginx](https://www.youtube.com/watch?v=GrHEVhGoqK0&list=PLFQSCKX4FTD7P0PYNTFkUA3Dd2BhatadZ&index=3&t=13s)
2. [文章:v2ray+ws+tls+nginx手动及一键脚本](https://briteming.blogspot.com/2019/01/v2ray-websocket-web-nginx.html)
3. [申请免费域名froomon](https://www.freenom.com/)
4. [免费cdn cloudflare](https://www.cloudflare.com/)
5. [google云 gcp](https://cloud.google.com/)
6. [ping测试](http://ping.chinaz.com/)