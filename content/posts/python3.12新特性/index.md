---
title: Python3.12版本新特性
date: 2023-11-01
categories: ["python"]
---


python 官方在 2023年10月2日发布了新的python版本3.12，这里记录一下其中比较重要的更新内容。

## 语法的改变之处

1. 取消了f-string语法的限制

    - 引号允许重用

        ```python
        # 3.12允许下面的语法，但是在前面的版本该语法会报错，必须更改不同的引号，比如 f"{'a' + 'b'}"
        f"{"a" + "b"}"
        ```
        
        受限于引号无法重用，所以虽然之前的版本并未对f-string的语法做任何限制，但是f-string语法是无法实现任意嵌套的。

        ```python
        # 在3.13中，可以无限层嵌套f-string
        f"{f"{f"{f"{1+2}"}"}"}"
        ```

    - 允许在f-string中使用多行表达式和添加注释

        ```python
        # 3.12允许下面的语法，但是下面的语法在之前的python版本中会报错
        f"Hello, {", ".join(
            "xiaoming"  # xiaoming
            "xiaohong"  # xiaohong
        )}"
        ```

    - 允许在f-string中使用字符 `\`
    
        这使得f-string支持unicode转义，能够在f-string使用更丰富的字符。下面是python官网提供的一个示例。

        ```python
        songs = ["Eden", "Alkaline", "Ascensionism"]
        print(f"This is the playlist: {"\N{BLACK HEART SUIT}".join(songs)}")
        ```
    - f-string报错信息更加精准

        ```python
        # 之前版本的报错信息
        my_string = f"{x z y}" + f"{1 + 1}"
            File "<stdin>", line 1
                (x z y)                               <========== 这里其实并不知道错误的确切位置，而是人为的使用括号进行了标注
                 ^^^
        SyntaxError: f-string: invalid syntax. Perhaps you forgot a comma?

        # 3.12版本的报错信息
        my_string = f"{x z y}" + f"{1 + 1}"
            File "<stdin>", line 1
                my_string = f"{x z y}" + f"{1 + 1}"   <========== 新版本得益于使用PEG解释器来解析f-string，能够更加精准的定位错误
                               ^^^
        SyntaxError: invalid syntax. Perhaps you forgot a comma?
        ```

2. 新的类型标注语法

    - 增加了一种更紧凑的泛型类和泛型函数的创建语法

        ```python
        def max[T](args: Iterable[T]) -> T:
            ...

        class list[T]:
            def __getitem__(self, index: int, /) -> T:
                ...

            def append(self, element: T) -> None:
                ...
        ```

        新增加的语法能够更加方便的创建泛型，代码也更加自然一些。

    - 可以使用 `type` 来给某个类型声明别名
    - 
        ```python
        type Point = tuple[float, float]
        ```

        基于这个新增加的语法，可以给一些复杂的类型声明一个别名，能够是代码更加简洁，使用起来也会更方便一些。

    - 可以使用 `typing` 中的 `TypedDict` 和 `Unpack` 对 `**kwargs` 进行更精确的类型标注
        ```python
        from typting import TypedDict, Unpack

        class Movie(TypedDict):
            name: str
            year: int
        
        def foo(**kwargs: Unpack[Movie]):
            ...
        ```
    
    - `typing` 模块中新增加 `override` 装饰器 ，用来向类型检查器说明被装饰方法为重写方法。

        ```python
        from typing import override

        class Base:
            def get_color(self) -> str:
                return "blue"
        
        class GoodChild(Base):
            @override
            def get_color(self) -> str:
                return "yellow
        ```

> 下面的内容目前使用的不多，暂时先不做详细的展开。
>


## 标准库的改进之处

### 新增特性

1. pathlib.Path 类现在支持子类化
2. os 模块获得了多项针对 Windows 支持的改进
3. 在 sqlite3 模块中添加了 命令行界面。
4. 基于 运行时可检测协议 的 isinstance() 检测获得了 2 至 20 倍的提速
5. asyncio 包的性能获得了多项改进，一些基准测试显示有 75% 的提速。
6. 在 uuid 模块中添加了 命令行界面。
7. 由于 PEP 701 中的更改，通过 tokenize 模块生成令牌（token）的速度最多可提高 64%。

### 移除部分标准包

1. 移除 `distutils ` 包；
2. 使用 `venv` 创建的虚拟环境中不再预装 `setuptools`；
3. 移除了 `asynchat`、`asyncore` 和 `imp` 模块。

## python解释器改进

1. 引入了解释器级的 `GIL`。
   
   该项改进使得现在可以创建带有独立的解释器级 `GIL` 的子解释器，将允许 Python 程序充分利用多个 CPU 核心，此特性目前仅能通过 C-API 使用，相应的 Python API 预计将在 3.13 中添加。

2. 定义了一个新的低开销的事件监控工具。
3. 针对 `NameError`, `ImportError` 和 `SyntaxError` 异常，优化了错误提示。

更多详细的内容，可以参考[这里](https://docs.python.org/zh-cn/3/whatsnew/3.12.html)