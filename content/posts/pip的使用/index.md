---
title: pip
date: 2023-10-07
categories: ["编程"]
---


pip是 [Python的包安装工具](https://packaging.python.org/en/latest/guides/tool-recommendations/)，
用户可以使用该工具安装 [pypi（Python包索引）](https://pypi.org/) 或
其他源（如 [清华源](https://pypi.tuna.tsinghua.edu.cn/simple/)）上的包。

从本质上来说，pip其实就是一个Python的一个包，只不过这个包可以用来管理Python的其他包，
它支持从不同的包索引源安装指定版本的包，即pip支持Python包的版本控制。

此外，pip还是一个命令行程序，在安装完pip后，会在系统中添加上pip命令，
此时可以直接在终端执行pip命令进行包的安装与管理等。

## 安装

### 自动安装

一般情况下，当用户使用以下两种方式之一配置Python环境时，pip会自动安装好。

- 用户使用 [虚拟环境](https://packaging.python.org/en/latest/tutorials/installing-packages/#creating-and-using-virtual-environments)；
- 用户使用从 [Python官网](https://www.python.org/) 下载的Python；

> [pip官网](https://pip.pypa.io/en/stable/installation/) 还提到了一种情况（ `using Python that has not been modified by a redistributor to remove ensurepip`），本人不太熟悉，暂时没有列到这里。

### 手动安装

如果你的环境中没有pip，可以使用以下两种方式进行安装。

- ensurepip
- get-pip.py

#### ensurepip

[ensurepip](https://docs.python.org/3/library/ensurepip.html#module-ensurepip) 是Python的一个内置模块，可以用来安装pip，只需执行以下命令即可。

```shell
python -m ensurepip --upgrade
```

#### get-pip.py

[get-pip.py](https://github.com/pypa/get-pip) 是官方提供的一个Python脚本，它使用一些引即可导逻辑（bootstrapping logic）来安装pip。

具体安装方法为：

1. 从 <https://bootstrap.pypa.io/get-pip.py> 下载脚本；
2. 切换到该脚本所在目录，使用Python运行该脚本。

```shell
python get-pip.py
```

安装完成之后，你可以通过使用下述命令检查pip是否已经安装成功。

```shell
python -m pip --version
# 你也可以直接使用pip命令查看版本信息
pip -V
```

## 更新

pip的更新比较容易，只需要执行一条命令即可。

```shell
python -m pip install --upgrade pip
```

## pip的基本使用

### 安装包

使用pip安装包的方式有三种，分别是通过Pypi安装、通过github安装以及通过包文件（distribution file）安装。

#### Pypi安装

这种方法是最简单最常用的Python包安装方法。

```shell
pip install package_name
```

>
> 可以通过在package_name后加上 `==x.y.z`、 `>=x.y.z`、 `<=x.y.z`、 `>x.y.z`、 `<x.y.z` 实现对包版本的控制。
>

#### github安装

```shell
pip install git+https://github.com/pypa/package_name.git@main
```

#### 包文件安装

通过包文件安装支持 `source distribution` 和 `wheel distribution` 两种文件。

```shell
pip install package_name-1.0.tar.gz
# 或
pip install package_name-1.0-py3-none-any.whl
```

>
> 除了以上三种方式外，在实际的Python项目开发中，经常会使用 `requirements.txt` 文件来实现对Python包版本的批量控制。
>
> 例如，在一个项目中你需要1.8.1版本的 ``alembic`` 库和0.79.1版本的 ``fastapi`` 库，你可以创建一个 ``requirements.txt`` 文件，文件内容如下：
>
> ```text
> alembic==1.8.1
> fastapi==0.79.1
> ```
>
> 然后通过下面的 `pip` 命令的 `-r` 参数进行指定版本包的批量安装。
>
> ```shell
> pip install -r requirements.txt
> ```
>

### 更新包

```shell
pip install --upgrade sampleproject
```

### 卸载包

```shell
pip uninstall sampleproject
```

## 换源

pip换源对于国内的Python用户来说，是必须掌握的一项基本技能。

之所以国内用户要进行pip换源，是因为pip默认的包下载源（ `Pypi` ）服务器是位于国外的，
当国内用户从该服务器上进行包下载安装时，受限于网络问题，往往下载速度会特别慢，
尤其是一些Python第三方库又比较大，往往会在下载时直接断掉，无法成功安装，体验极差。

为了解决这一问题，国内有一些机构就针对国内用户自己搭建了镜像源，方便国内用户的使用，比较常用的镜像源有：

- 清华大学：<https://pypi.tuna.tsinghua.edu.cn/simple>

- 阿里云：<https://mirrors.aliyun.com/pypi/simple>

- 中国科学技术大学 <https://pypi.mirrors.ustc.edu.cn/simple>

- 豆瓣：<http://pypi.douban.com/simple>

比较常用的更换镜像源方法有3种。

### 1. 临时性换源

使用 `pip` 的 `-i` 参数可以指定本次命令执行时从何处下载包文件，例如：

```shell
# 此处示例中使用的是清华源，你可以根据自己的喜好修改成其他的镜像源
pip install pandas -i https://pypi.tuna.tsinghua.edu.cn/simple
```

> 注意：这种方法只是临时性修改，仅对本次安装过程有效。
>

### 2. 修改配置文件

`pip` 的配置文件可以分为3个级别，分别是 **全局级别（Global）**、 **用户级别（User）**和 **站点级别（Site）**。

全局级别是最高的级别，全局级别的配置文件，对整个机器上的 `pip` 都生效。

用户级别介于全局级别和站点级别之间，仅对当前用户的 `pip` 生效。

站点级别的等级最低，仅对当前站点或者说项目生效，一般是指某个项目虚拟环境中的 `pip`。

各个级别配置文件的对应的路径信息如下表所示。

|  级别  |             级别             |          linux          |                    macos                    |
| :----: | :--------------------------: | :---------------------: | :-----------------------------------------: |
| global | `C:\ProgramData\pip\pip.ini` |     `/etc/pip.conf`     | `/Library/Application Support/pip/pip.conf` |
|  user  |   `%APPDATA%\pip\pip.ini`    |  `$HOME/.pip/pip.conf`  |            `$HOME/pip/pip.conf`             |
|  site  |   `%VIRTUAL_ENV%\pip.ini`    | `$VIRTUAL_ENV/pip.conf` |           `$VIRTUAL_ENV/pip.conf`           |

不同等级的配置文件在加载时，会按照等级由高到低的次序加载，
当不同等级配置文件配置内容存在冲突时，后加载的内容会覆盖前面加载的内容，
由此实现优先使用Site级别配置，然后使用User级别配置，如果都没有配置，则选用Global级别的配置。

换源时，只需根据自己的需要，修改对应级别的配置文件即可。具体修改内容为在配置文件中添加如下内容即可：

```text
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
[install]
trusted-host = https://pypi.tuna.tsinghua.edu.cn
```

`index-url` 参数指定了镜像源，添加`trusted-host`参数是为了标记信任该镜像源，否则可能会在使用https镜像源时触发警告信息。

### 1. 使用命令行修改

从 `pip10.0.0` 开始， `pip` 提供了 `config` 子命令用来改配置，使用 `pip` 命令的 `config` 方法便可以实现对镜像源的永久修改。

```shell
pip config set global.index-url https://pypi.tuna.tsinghua.edu.cn/simple
```

如果要换回默认的Pypi源，可以使用下面的命令。

```shell
pip config unset global.index-url
```

## 其他常用命令

### pip list

列出当前环境中已经安装的包。

### pip show \<a_package\>

查看 `<a_package>` 的基本信息。

> 注意： `a_package` 是一个已经安装的包。
>

### pip freeze

将当前环境安装的所有包及其对应版本输出到一个requirements文件中。

### pip search \<query\>

从 `Pypi` 源搜索包含 `<query>` 的包。

## 参考资料

1. [pip官方介绍文档](https://pip.pypa.io/en/stable/)
