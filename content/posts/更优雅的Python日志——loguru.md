---
title: 更优雅的Python日志——loguru
date: 2023-10-13
categories: ["python"]
---

python内置了 `logging` 模块用来记录日志，但是 `logging` 模块使用起来有诸多不便：

- 多线程安全，但是多进程不安全；
- 配置繁琐，无法做到“开箱即用”。

为了解决上述问题，可以尝试使用 `loguru` 模块，该模块使用者非常友好，可以帮助开发人员更加快速高效的完成python日志的记录。

相较于 `logging` 模块，`loguru` 具有以下特点：

- 配置方便，开箱即用；
- 支持日志分卷、日志压缩以及自动清理；
- 支持异步，多进程、多线程安全；
- 报错日志可以更加详细地记录；
- 可根据需要定制结构化日志；
- 支持自定义日志等级。

下面介绍一下如何使用 `loguru` 模块。

## 安装

`loguru` 安装非常简单，直接使用 `pip` 工具安装即可。

```bash
pip install loguru
```

## 使用

关于 `loguru` 的使用方法，这里按场景进行介绍。

> `loguru` 中核心的概念是 `logger`， 核心的方法是 `add` ，请在下面的场景示例代码中重点关注这两点。

### 打印日志到终端窗口

```python
from loguru import logger
logger.info("Hello, world!")
logger.info("Hello, {name}!", name="everyone")  # 支持braces style
```

![image-20231013100017818](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20231013100017818.png)

### 保存日志到文件

```python
from loguru import logger

logger.add("log.log")
logger.info("Hello, world!")
logger.info("Hello, {name}!", name="everyone")
```

将日志输出到文件，只需要使用 `add` 方法，添加一个文件名即可，如果文件不存在，程序将会自动创建（包括创建不存在的目录）。

![image-20231013101544570](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20231013101544570.png)

注意：执行 `from loguru import logger` 后，程序会默认创建一个将日志展示在终端窗口的 `handler` ，上面代码中的 `add` 方法会在此基础上在创建一个输出到 `log.log` 的 `handler` ，如果不想在终端展示日志，可以使用下面的代码。

```python
from loguru import logger

logger.remove()  # 移除所有的日志输出
logger.add("log.log")
logger.info("Hello, world!")
logger.info("Hello, {name}!", name="everyone")
```

`logger.remove()` 将移除前面的所有 `handler` ，所以在使用 `add` 方法后，便只有输出到文件的一个 `handler` ，终端将不会显示日志信息。

如果想重新输出到终端，可以添加代码 `logger.add(sys.stdout)` 。

### 日志分卷

在实际记录日志时，可能需要按某些条件来对日志进行切分，如每天的日志记录在一个日志文件中。 `loguru` 可以很方便的实现这一需求。

```python
from loguru import logger

logger.add(
    "log_{time:YYYY-MM-DD}.log",
 rotation="00:00"
)
logger.info("Hello, world!")
logger.info("Hello, {name}!", name="everyone")
```

![image-20231013104043169](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20231013104043169.png)

上面的程序将会在每天 `00:00` 重新创建一个日志文件保存当天的日志信息。

日志文件名的格式可以自己定义，如 `log_{time:YYYY-MM-DD}.log`。重新创建新文件的时间也可以通过 `rotation` 参数定义，通过该参数可以设置重新生成新日志文件的条件（支持**按文件大小**和**时间**两种条件），下面是一些例子。

```python
logger.add("log.log", rotation="50M")      # 文件超过50M则另起一个文件
logger.add("log.log", rotation="00:00")    # 每天00:00另起一个文件
logger.add("log.log", rotation="1 week")   # 每周另起一个文件
logger.add("log.log", rotation="10 days")  # 每10天另起一个文件
```

### 自定义日志格式

`loguru` 支持自定义日志格式，只需要在 `add` 方法中添加 `format` 参数进行控制即可。

```python
import sys
from loguru import logger

logger.remove()
logger.add(sys.stdout, format="{time:YYYY-MM-DD at HH:mm:ss} | <red>{level}</red> | <green>{file}</green> | <b><i>{message}</i></b>")
logger.info("Hello, world!")
logger.info("Hello, {name}!", name="everyone")
```

![image-20231013105819246](https://littletom.oss-cn-nanjing.aliyuncs.com/image-20231013105819246.png)

在 `format` 中，可以通过 `{param}` 的形式设置内容，通过 `<tag></tag>` 的形式设置格式。

常见的内容参数有：

| 参数      | 含义     |
| --------- | -------- |
| time      | 时间     |
| level     | 日志等级 |
| file      | 文件名   |
| message   | 日志信息 |
| path      | 文件路径 |
| function  | 函数     |
| line      | 行数     |
| module    | 模块     |
| process   | 进程信息 |
| thread    | 线程信息 |
| tzinfo    | 时区信息 |
| exception | 异常信息 |

常见标签格式可以参考[这里](https://loguru.readthedocs.io/en/stable/api/logger.html#color)。

### 其他常用配置

```python
# retention 参数可以设置日志保存时间，到时间后日志会自动清理，注意与 rotation 区分
logger.add("log.log", retention="10 days")

# compression 可以对关闭的日志（如 rotation 处理的日志）进行压缩，节约空间
logger.add("log.log", compression="zip")

# enqueue=True 可以支持异步写入，实现多进程安全
logger.add("log.log", enqueue=True)

# filter 参数可以设置日志的过滤规则
# 过滤不包含 hello 的日志
logger.add("log.log", filter=lambda x: "hello" not in x["message"])

# encoding 设置编码方式
# level 设置日志等级，大于该等级的日志会被记录
logger.add("log.log", encoding="utf-8", level="INFO")
```

### 日志配置推荐

作为参考，这里给出一个简单的日志配置。

```python
import os
import time
from loguru import logger

logger_dir = "./log"  # 这里是日志的存放目录，需要根据你的项目进行修改

log_path = os.path.join(logger_dir, f'{time.strftime("%Y%m")}', '{time:YYYY_MM_DD}.log')
logger.add(
    log_path,
    rotation="00:00",
    retention="30 days",
    enqueue=True,
    encoding="utf-8",
    level="INFO"
)
```

`loguru`的特性还有很多，更多关于 `loguru` 的用法可以直接去官方文档查看。

## 参考连接

- [loguru官方文档](https://loguru.readthedocs.io/en/stable/overview.html)

- [Python 第三方日志框架loguru使用](https://www.cnblogs.com/wenqiangit/p/11194551.html)
