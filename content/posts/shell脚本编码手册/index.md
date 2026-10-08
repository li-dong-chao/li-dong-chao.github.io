---
title: shell脚本编码手册
date: 2023-10-03
categories: ["linux"]
---

## 比较符号

|    关系    | 符号  |      英文解释      |
| :--------: | :---: | :----------------: |
|    大于    |  -gt  |    greaterthan     |
|    小于    |  -lt  |      lessthan      |
| 大于或等于 |  -ge  | greaterthanorequal |
| 小于或等于 |  -le  |  lessthanorequal   |
|   不相等   |  -ne  |      notequal      |
|    相等    |  -eq  |       equal        |

## shell脚本打印带颜色日志

```shell
SETCOLOR_SUCCESS="echo -n \\033[1;32m"
SETCOLOR_FAILURE="echo -n \\033[1;31m"
SETCOLOR_WARNING="echo -n \\033[1;33m"
SETCOLOR_NORMAL="echo -n \\033[0;39m"

LogWarnMsg()
{
    time=$(date "+%D %T")
    $SETCOLOR_WARNING
    echo "[$time] : WARN    : $*"
    $SETCOLOR_NORMAL
}

LogSucMsg()
{
    time=$(date "+%D %T")
    $SETCOLOR_SUCCESS
    echo "[$time] : SUCCESS : $*"
    $SETCOLOR_NORMAL
}

LogErrorMsg()
{
    time=$(date "+%D %T")
    $SETCOLOR_FAILURE
    echo "[$time] : ERROR   : $*"
    $SETCOLOR_NORMAL
}

LogSucMsg "success"
LogWarnMsg "warning"
LogErrorMsg "error"
```
