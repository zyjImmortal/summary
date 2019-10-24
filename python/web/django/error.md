### `ImproperlyConfigured: mysqlclient 1.3.13 or newer is required`

##### 问题背景
已经安装好了 pymysql，但是在命令行执行 python3 manage.py migrate 进行数据迁移时报如下错误：
（当然，你也可能是在执行其它操作时遇到类似错误
```

ImproperlyConfigured: mysqlclient 1.3.13 or newer is required
```
安装pymysql，在项目根目录下__init__.py文件里执行下面代码
```
import pymysql
pymysql.install_as_MySQLdb()
```

##### 迁移继续报错
```
File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/importlib/__init__.py", line 127, in import_module
    return _bootstrap._gcd_import(name[level:], package, level)
  File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/django/db/backends/mysql/base.py", line 36, in <module>
    raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you have %s.' % Database.__version__)
django.core.exceptions.ImproperlyConfigured: mysqlclient 1.3.13 or newer is required; you have 0.9.3.
```

解决方式：
注释掉base.py文件里的光宇版本判断的代码

```
 version = Database.version_info
 #if version < (1, 3, 13):
 #    raise ImproperlyConfigured('mysqlclient 1.3.13 or newer is required; you     have %s.' % Database
```

##### 继续迁移，还有报错,
```
File "/Library/Frameworks/Python.framework/Versions/3.7/lib/python3.7/site-packages/django/db/backends/mysql/operations.py", line 146, in last_executed_query
    query = query.decode(errors='replace')
AttributeError: 'str' object has no attribute 'decode'
```
在python里字符串只有encode方法，转换成bytes,byte只有decode方法转换成str，所有只需修改源代码中query.encode就可以
```
def last_executed_query(self, cursor, sql, params):
    # With MySQLdb, cursor objects have an (undocumented) "_executed"
    # attribute where the exact query sent to the  #database is saved.
    # See MySQLdb/cursors.py in the source distribution.
    query = getattr(cursor, '_executed', None)
    if query is not None:
        query = query.encode(errors='replace')	
        # 这里把 decode 改为 encode
    return query

```