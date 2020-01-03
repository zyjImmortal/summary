### jwt: json web token



两种实现方式

jws-signature   签名

header payload signature

Jwe 加密

```python
def generate_jwt(payload, expiry, secret=None):
    """
    生成jwt,json web token
    :param payload: dict 载荷
    :param expiry: datetime 有效期
    :param secret: 密钥
    :return: jwt
    """
    _payload = {'exp': expiry}
    _payload.update(payload)
	  if not secret:
        secret = current_app.config['JWT_SECRET']
    token = jwt.encode(_payload, secret, algorithm='HS256')
    return token.decode()
def verify_jwt(token, secret=None):
    """
    检验jwt
    :param token: jwt
    :param secret: 密钥
    :return: dict: payload
    """
    if not secret:
        secret = current_app.config['JWT_SECRET']
    try:
        payload = jwt.decode(token, secret, algorithm=['HS256'])
    except jwt.PyJWTError:
        payload = None
    return payload
```



### Jws 的保护机制说明

头部			 payload 			签名

base64  		base64           HS256对前面的两个base64加密的和秘钥一起加密，作为签名



