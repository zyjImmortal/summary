* 获取响应内容

response.content 获取到的是byte类型，没有指定编码类型，需要decode()成字符串类型，

response.content.decode("指定编码类型")

response.text返回是str类型，根据http头部指定编码尝试解码，可以通过response.encoding = "utf-8"来解码

一般情况下，名词是属性，动词是方法