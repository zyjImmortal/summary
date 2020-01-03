`os.``fspath`(*path*)

返回路径的文件系统表示。

如果传入的是 [`str`](https://docs.python.org/zh-cn/3/library/stdtypes.html#str) 或 [`bytes`](https://docs.python.org/zh-cn/3/library/stdtypes.html#bytes) 类型的字符串，将原样返回。否则 [`__fspath__()`](https://docs.python.org/zh-cn/3/library/os.html#os.PathLike.__fspath__) 将被调用，如果得到的是一个 [`str`](https://docs.python.org/zh-cn/3/library/stdtypes.html#str) 或 [`bytes`](https://docs.python.org/zh-cn/3/library/stdtypes.html#bytes) 类型的对象，那就返回这个值。其他所有情况则会抛出 [`TypeError`](https://docs.python.org/zh-cn/3/library/exceptions.html#TypeError) 异常。

*3.6 新版功能.*