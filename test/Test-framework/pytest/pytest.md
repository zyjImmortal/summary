

* 执行多个测试用例

在当前文件夹和子文件夹中，pytest会执行所有符合`test_*.py`和`*_test.py`命名规则的文件

在py文件中，查找类外部以test开头的测试方法，查找以Test开头的测试类内部的已test开头的方法

可以指定非递归目录

```
[pytest]
norecursedirs = .svn _build tmp*
```

如果没有使用参数指定目录，会从当前目前开始查找

* 指定测试用例执行

  * 执行指定模块中的测试用例

    pytest test_mod.py

  * 执行指定目录下的测试用例

    pytest testing/

  * 执行文件名、类名、或者函数名中包含特定关键字的测试用例

* 测试依赖

  