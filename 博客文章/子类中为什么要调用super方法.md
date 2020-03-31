super函数用于在子类中显示调用父类的方法，一般情况下会在子类中定义与父类相同的属性和方法，依次来实现子类自己的行为。

super函数的使用方式

* 在init方法中使用

如果子类没有定义自己的初始化函数，父类的初始化会被默认调用，在这种情况下如果要实例化子类的对象，就只能传入父类的初始化函数对应的参数。

```
class Animal:

    def __init__(self, name):
        self.name = name
class Bird(Animal):
    pass
    
    
Traceback (most recent call last):
  File "/Users/zhouyajun/PycharmProjects/geek/python/super_demo.py", line 21, in <module>
    bird = Bird()
TypeError: __init__() missing 1 required positional argument: 'name'    
```

如果子类定义了初始化函数，并且在子类中没有显示调用父类的初始化函数，则父类的属性不会被初始化,意味着只能访问子类自己的属性，而不能访问父类当中的属性

```python
class Animal:
    def __init__(self, name):
        self.name = name
        
class Bird(Animal):
    def __init__(self, age):
        self.age = age
        print(self.name)

bird = Bird(12)

Traceback (most recent call last):
  File "/Users/zhouyajun/PycharmProjects/geek/python/super_demo.py", line 24, in <module>
    bird = Bird(12)
  File "/Users/zhouyajun/PycharmProjects/geek/python/super_demo.py", line 22, in __init__
    print(self.name)
AttributeError: 'Bird' object has no attribute 'name'
```

如果子类定义了自己的初始化函数，在子类中显示调用父类，子类和父类的属性都会被初始化，一般会在子类的构造方法中通过super方法显示调用父类的构造方法，保证父类的属会被初始化。

* 在其他方法中使用



```python
import logging
class LoggingDict(dict):

    def __setitem__(self, key, value):
        logging.info("setting {} to {}".format(key, value))
        super(LoggingDict, self).__setitem__(key, value)
```

上面实现一个LoggingDict类继承内置的dict类，然后重写了`__setitem__`方法，在设置属性值的时候，先打印操作日志，然后再调用父类的`__setitem__`来设置值。在调用super的时候传入了实例的类型，和当前的实例对象，也就是说在父类调用自己的方法的时候，是操作的传递过来的实例。

