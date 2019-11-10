### drf的视图

#### 视图继承关系

```django.views.generic.base.View```所有视图类的基类，有Django提供,内置了最基础的方法

```rest_framework.views.APIView```继承至```View```并对其进行加强，提供权限控制、限流等功能

```rest_framework.generics.GenericAPIView```继承至```APIView```又增加了序列化和反序列化的功能

### drf提供的扩展类

根据视图是否要对数据进行操作以及操作的方式

* get请求中对数据操作
* post请求
* put请求

