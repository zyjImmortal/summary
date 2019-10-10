避免使用assertTrue或者assertFalse这种方式断言，使用assertThat，可以是断言失败信息更加明确，如果非要使用可以加.as()方法，明确失败信息

实际值和期望值变量命名，尽量能体现区别例如，

``````java
Advert actualAdvert = advertList.get(0)
Advert expectAdvert = new Advert()
``````

需要制定实际参数值的时候，不要用随机值，要用确定的值，比如订单价格，如果需要检验多个值的时候可以使用Junit的Parameterized参数化测试

case要短小精悍，只测该测的，比如测试一个逻辑，只验证和这个逻辑相关的返回值，不去做过多校验，其他参数的校验让其他的case去cover



