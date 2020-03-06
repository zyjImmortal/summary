

```python
a = ('a', 'b','c)

c = copy.copy(a)

d = copy.deepcopy(a)

if c == d:
     print("c和d的值相等")
if id(c) == id(d):
    print("c和d的地址相等")
	
```



python测试开发面试之装饰器

用装饰器实现统计函数运行时间的功能

