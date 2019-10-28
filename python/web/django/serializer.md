# drf

## 序列化器的分类

#### 模型序列化器

#### 根序列化器

####模型序列化器

### 自定义序列化器

在类视图中，对参数进行校验，实现一个根序列化器，

```python
class CheckImageCodeSerializer(serializers.Serializer):
    """图片验证码校验序列化器"""
    # mobile 通过路由的正则表达式来匹配同时也可以校验
    image_code_id = serializers.UUIDField()
    text = serializers.CharField(min_length=4, max_length=4)
    # 带下划线只针对某个字段，不带下划线针对所有字段
    def validate(self, attrs: dict):
        """校验图片验证码"""
        redis_conn = get_redis_connection("verify_codes")
        real_image_code = redis_conn.get("img_%s" % attrs['image_code_id'])
        if real_image_code is None:
            # 序列化器中是直接返回异常
            raise serializers.ValidationError("无效的图片验证码")
        real_image_code = real_image_code.decode()
        if real_image_code.lower() != attrs['text'].lower():
            raise serializers.ValidationError("图片验证码错误")
        
```

这个是一个进行发送验证码的类视图所需的序列化器，需要对图片验证码、验证码ID以及手机号进行校验，

手机号可以通过路由的正则表达式匹配的时候进行校验，图片验证码存在```redis```中，在进行是否合法校验时，需要从根据手机号```mobile```来查询到，那么就要在```CheckImageCodeSerializer```中获取到手机号，先来看那一下在类视图中如何使用自定义的序列化器，

```python
class SMSCodeView(GenericAPIView):
  # 指定序列化器,serializer_class属性时GenericAPIView的一个类属性
    serializer_class = CheckImageCodeSerializer

    def get(self, request, mobile):
      	# 获取序列化器的实例
        serializer = self.get_serializer()
        serializer.is_valid(raise_exception=True)
```

通过```self.get_serializer()```获取序列化器实例的时候，```drf```还给序列化器添加了额外的参数，看一下源码

```python
  def get_serializer_context(self):
        """
        Extra context provided to the serializer class.
        """
        return {
            'request': self.request,
            'format': self.format_kwarg,
            'view': self
        }  
  def get_serializer(self, *args, **kwargs):
        """
        Return the serializer instance that should be used for validating and
        deserializing input, and for serializing output.
        """
      	# 获取序列化器 ，返回GenericAPIView的类变量
        serializer_class = self.get_serializer_class()
        # self.get_serializer_context()返回了一个字典，有三个属性，包括
        # request，这次请求的所有信息
        # format，前端格式
        # view 就是当前类视图的实例
        kwargs['context'] = self.get_serializer_context()
        # 返回序列化器实例
        return serializer_class(*args, **kwargs)
      
```

自定义的序列化器继承至```Serializer```，它又继承至```BaseSerializer```根序列化器，在序列化器实例化的时候

会将传入的```context```保存到自己的实例属性中,所以在序列化器中可以通过```self.context```访问上下文信息。

**注意：**序列化器不是只能为数据库模型类定义，也可以为非数据库模型类的数据定义，```Serializer```是独立于数据库之外的存在

### 序列化器的实例化

