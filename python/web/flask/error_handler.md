## 异常处理器的作用
## 实现
```python
    @app.errorhandler(Exception)
    def handler(e):
        if isinstance(e, APIException):
            return e
        if isinstance(e, HTTPException):
            code = e.code
            msg = e.description
            error_code = 20000
            return APIException(msg, code, error_code)
        else:
            if not app.config['DEBUG']:
                import traceback
                app.logger.error(traceback.format_exc())
                return UnknownException()
            else:
                raise e
```
## 注册
## 异常类封装