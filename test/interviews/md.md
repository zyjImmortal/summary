# 都有哪些种类的配置文件

* pytest.ini： pytest的主配置文件，可以改变pytest的默认行为
* conftest.py：是本地插件库，其中的hook函数和fixture将作用于该文件所在的目录以及所有子目录
* tox.ini: 如果使用到tox工具，也会有这个配置文件，可以把pytest的配置都写在tox.ini文件里，这样就不用同时写两个配置文件了
* setup.cfg: 也采用的是ini文件的格式，而且可以影响setup.py的行为，使用python setup.py test运行所有测试用例，

# pytest 的配置选项

使用pytest --help可以查看pytest支持的所有配置的选项，以下只截取部分

```python
 markers (linelist):   markers for test functions
  empty_parameter_set_mark (string):
                        default marker for empty parametersets
  norecursedirs (args): directory patterns to avoid for recursion
  testpaths (args):     directories to search for tests when no files or directories are given in the
                        command line.
  usefixtures (args):   list of default fixtures to be used with this project
  python_files (args):  glob-style file patterns for Python test module discovery
  python_classes (args):
                        prefixes or glob names for Python test class discovery
  python_functions (args):
                        prefixes or glob names for Python test function and method discovery
  disable_test_id_escaping_and_forfeit_all_rights_to_community_support (bool):
                        disable string escape non-ascii characters, might cause unwanted side effects(use
                        at your own risk)
  console_output_style (string):
                        console output: "classic", or with additional progress information ("progress"
                        (percentage) | "count").
  xfail_strict (bool):  default for the strict parameter of xfail markers when not given explicitly
                        (default: False)
  addopts (args):       extra command line options
  minversion (string):  minimally required pytest version
```

* addopts

当在命令行运行测试用例的时候，通常会指定多个命令行参数，但是总不能每次运行的时候都要输入一遍，这个时候我们就可以利用addopts这个选项，将一写常用的参数聚合到一起，执行的时候不用显示指定，只需要pytest即可。

```
[pytest]addopts = - -l --tb=short --strict

```

rsxX表示pytest会显示所有测试用例被跳过-s、预计失败-x、预计失败单通过-X的原因-s，-l表示pytest显示失败用例堆栈中的局部变量的信息，--tb=short 表示简化堆栈信息，只保留文件和行数，

--strict 选项表示禁止使用未在配置文件注册过的标记

* markers

将自定义的标记注册进pytest中，如果使用里--strict选项，在遇到拼写错误或者未注册的标记的时候就会报错。如果不注册不使用--strict也能用，但是当在自定义的标记出现拼写错误的时候，在默认情况下不会引起程序错误，通过注册标记的方式，将错误提前抛出，方便问题处理。

```
[pytest]
markers =      smoke: 用于冒烟测试      get
```

* minversion

指定pytest最小版本号

* norecursedirs

pytest搜索测试用例的时候，会递归遍历所有子目录，但是随着产品的迭代有些用例已经不适合或者不必要在运行了，这时我们想告诉pytest，这些用例就不要执行了。

通过指定norecursedirs可以实现

```
[pytest]
norecursedirs = .* venv src *.egg dist build
```

其实类似.gitignore文件的功能，忽略调某些文件不进行版本控制

* testpaths

norecursedirs告诉pytest那些路径不用访问，testpaths告诉pytest去哪里访问。testpaths是一系列相对于根目录的路径，用于限定测试用例的搜索范围，当使用tox来管理测试用项目时，这时的项目目录是自定义的，此时一般tox.ini和tests用例文件夹处于同级目录，就需要在tox.ini里指定测试目录

![深入理解自动化测试框架pytest的配置文件](http://p3.pstatp.com/large/pgc-image/1e70c00488f445bca7e8d048afccbad7)

```
[pytest]
testpaths = tests
```

* 更改测试搜索的规则

pytest根据一定的规则搜索并运行测试，标准的搜索规则如下：

1. 从一个多个目录开始查找，可以在命令行指定目录，如果未指定则以当前目录为根目录
2. 在该目录或者所有子目录下递归查找测试模块
3. 测试模块指的是文件名以test_*.py或*._test.py的文件
4. 在测试模块中查找以test_开头的函数名
5. 查找以Test开头的类，会筛选调有__ini__函数的类，在查找以test开头的方法

**python_classes**

一般情况下，pytest的测试搜索规则寻找以Test开头的测试类，而且这个类不能包含__ini__()函数，但是有时候会把测试类命名以Test结尾的类，比如***Test，这个时候就可以指定**python_classes的值来解决**

```
[pytest]python_classes = *Test Test* *Suit
```

**python_files**

和python_classes类似，可以更改测试模块的搜索规则，

```
[pytest]
python_files = *_test.py test_*.py
```

**python_fuctions**

修改测试方法的搜索规则

```
[pytest]  

python_fuctions = *_test test_*
```

* 禁用xpass

设置xfail_strict = true将会使得那些被标记为@pytest.mark.xfail但实际通过党的测试用例也会显示为失败。

**pytest的配置选项还有配置是否输出到Junit xml报告文件中，控制台日志输出，log文件输出配置等，大家可查阅官方文档继续学习!**