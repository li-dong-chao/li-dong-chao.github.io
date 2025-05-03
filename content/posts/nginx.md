---
title: nginx使用手册
date: 2023-10-02
categories: ["linux"]
---

## nginx简介

Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，
其特点是占有内存少，并发能力强，
事实上nginx的并发能力在同类型的网页服务器中表现是比较好的。
Nginx专为性能优化而开发，性能是其最重要的考量，实现上非常注重效率。
在一些场景下，nginx能够支持高达50000的并发连接数。


## nginx主要功能点

- 反向代理
- 负载均衡
- 动静分离

### 1. 反向代理

在了解反向代理之前，需要先了解一下正向代理的概念。

#### 正向代理

正向代理的基本结构如下：

![20230215_1](https://littletom.oss-cn-nanjing.aliyuncs.com/20230215_1.png)

该结构是国内用户访问google时一般用到的正向代理结构。

由于我们的个人PC电脑是无法直接直接访问google的，
因此我们往往需要通过一台能够访问google的服务器作为代理服务器，
当我们需要访问google时，通过先访问代理服务器，在由代理服务器访问google服务器的方式，
间接地实现对google的访问。

这种代理方式就叫做正向代理，它是通过在客户端(如PC)配置代理服务器，
然后通过代理服务器实现对互联网的访问。

可以发现，正向代理服务端没有做任何的配置操作，仅在客户端进行了代理服务器的配置。

下面来看一下反向代理是什么。

#### 反向代理

与正向代理不同，反向代理的配置操作发生在服务端。

下面将通过一个示意图来对反向代理进行介绍。

![20230215_2](https://littletom.oss-cn-nanjing.aliyuncs.com/20230215_2.png)

上图描述了反向代理的一般结构。
当客户端在访问服务端时，可能访问的只是服务端配置的代理服务器，
再由代理服务器决定将客户端的请求转发到哪台机器上进行处理。
请求处理结束，同样是通过代理服务器返回给客户端。
这样从客户端来看，代理服务器和实际进行请求处理的机器就是一个机器，
仅仅暴露了代理服务器的地址和端口，隐藏了实际进行处理的机器，这个过程就是反向代理。


## 安装nginx


### windows平台

win下可以直接去 [官网](https://nginx.org/en/download.html) 下载zip文件，解压后直接双击nginx.exe即可直接启动nginx。

![20230215_3](https://littletom.oss-cn-nanjing.aliyuncs.com/20230215_3.png)

启动完成后，在浏览器输入127.0.0.1，看到下面界面说明nginx已经安装成功并启动了。

![20230215_4](https://littletom.oss-cn-nanjing.aliyuncs.com/20230215_4.png)

>
> 如果希望全局使用，只需配置一下环境变量即可。
>

### linux平台

#### 1. 安装编译工具以及库文件

```shell
yum -y install make zlib zlib-devel gcc-c++ libtool  openssl openssl-devel
```

#### 2. 安装PCRE

安装pcre是为了让nginx支持Rewrite功能。

安装过程：

- 下载pcre安装包

你可以从 [这里](http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz) 直接下载到本地再上传到服务器 `/usr/local/src` 目录下；

也可以使用wget直接下载到服务器上。

```shell
cd /usr/local/src/
wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
```

- 解压

```shell
tar -zxvf pcre-8.35.tar.gz
```

- 编译安装

```shell
cd pcre-8.35
./configure
make && make install
```

- 验证是否安装成功

```shell
pcre-config --version
```

#### 3. 安装nginx

安装过程：

- 下载nginx安装包

你可以从 `官网`_ 直接下载到本地再上传到服务器 ``/usr/local/src`` 目录下；

也可以使用wget直接下载到服务器上。

```shell
cd /usr/local/src/
wget http://downloads.sourceforge.net/project/pcre/pcre/8.35/pcre-8.35.tar.gz
```

- 解压

```shell
tar -zxvf nginx-1.20.2.tar.gz 
```

- 编译安装

```shell
cd nginx-1.20.2
./configure --prefix=/usr/local/webserver/nginx --with-http_stub_status_module --with-http_ssl_module --with-pcre=/usr/local/src/pcre-8.35
make
make install
```

到此，nginx已经安装完成。


### Nginx基础命令

Nginx常用到的命令还是比较简单的，具体如下：

|       命令        |                                                   作用                                                   |
| :---------------: | :------------------------------------------------------------------------------------------------------: |
|   nginx -s stop   |  快速关闭Nginx，可能不保存相关信息，nginx在接收到该指令后会立即结束web服务，不管当前请求有没有处理完。   |
|  nginx -s reload  |                   修改了Nginx相关配置后，使用该命令重新加载配置信息，并重启nginx服务。                   |
|  nginx -s reopen  |                                            重新打开日志文件。                                            |
| nginx -c filename |                                   指定启动nginx服务时使用的配置文件。                                    |
|     nginx -t      | 不启动nginx服务，仅测试配置文件。nginx将检查配置文件的语法的正确性，并尝试打开配置文件中所引用到的文件。 |
|     nginx -v      |                                           显示 nginx 的版本。                                            |
|     nginx -V      |                                显示 nginx 的版本，编译器版本和配置参数。                                 |
