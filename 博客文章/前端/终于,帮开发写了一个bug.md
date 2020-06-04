### 写在文章的开头

最近项目比较紧，尤其前端的的需求比较多，作为一名测试，也会些vue，本着加快项目进度的美好想法，就自告奋勇的向组长承接了一部分开发的任务，其中有个需求需要在我们的广告管理后台新增一个上传图片的功能，需要先上传到七牛云，然后在将图片的地址传给服务端。

### bug的由来

在项目里我们使用的是Iview组件库的Upload组件，这个组件可以设置一个回调函数用于在文件上传成功后做一些处理

![image-20200518194057876](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589802061.png)

on-success属性可以用于指定上传成功后的回调函数，我就把图片上传到服务端的逻辑写在了这个回调函数中：

```js
handleSuccess(userId, res) {
  if (res.key) {
    let img
    if (SERVER_ENV === 'local' || SERVER_ENV === 'development') {
      img = `https://t-xxxx/${res.key}`
    } else {
      img = `https://xxxxx/${res.key}`
    }
    const params = {
      'id': userId,
      'sign_screenshot': img
    }
    const result = API.signScreenShot(params)
    const { status, data, msg } = result
    if (status !== 1000) {
      this.$message(msg)
    }
  }
}
```

看起来一切都还好，我就想看看功能好使不，上传了一张图片，可是确报错了

![image-20200518194603860](https://zyjblog.oss-cn-beijing.aliyuncs.com/1589802366.png)

报错显示key这个属性是undefined，chrome定位到的错误代码行是

```js
img = `https://t-xxxx/${res.key}`
```

这就让我很不痛快，我的第一反应就是，明明第一个if语句已经判断了key是存在，为什么进入if代码里确提示undefined，这不科学啊！一脸懵逼的我在if的前前后后都答应了日志，就更加的一脸懵逼了，居然真的在if前面是有的，进入if后就是undefined，这很明显已经脱离我的js认知范围了，就只能找前端同学了。

### 被开发虐的那一瞬间

前端同学拿过代码一看，不对呀这，你下面咋又定义了：

```js
const res = API.signScreenShot(params)
```

你这变量提升了哇，，？？？？？？？，，小朋友你为什么这么多问号，然后我就走开了。

### js的变量提升

* 什么是提升呢

声明变量都会被移动到各自作用域的最顶端,这个过程被称为提升

* es6的局部作用域

在ES6中，{}会被定义为一个块级作用域，在这个块级作用域内，使用const定义变量，那么定义就会被提升到块的最顶端。js在执行之前会先进行编译，编译完就会把变量的定义提升到作用域的顶部，上面我写的代码，const res就会出现在这里

```js
if (res.key) {
		const res
    let img
    if (SERVER_ENV === 'local' || SERVER_ENV === 'development') {
      img = `https://t-xxxx/${res.key}`
    } else {
      img = `https://xxxxx/${res.key}`
    }
    const params = {
      'id': userId,
      'sign_screenshot': img
    }
    res = API.signScreenShot(params)
    const { status, data, msg } = res
    if (status !== 1000) {
      this.$message(msg)
    }
  }
```

所以才会出现上面的报错，在块里，我只声明了，但是没有复制这个时候，就去使用了，肯定会报undefined。

### 修复

改一下后面的变量名，，很简单么，，有啥难的么，，！！

```js
const result = API.signScreenShot(params)
```

### 总结

终于bug被修复了，写js代码尽量避免变量名重复，不然未知的坑不少，多多小心！

开发到测试一人完成，心里也是美滋滋，学到不少，感谢前端同学，感谢自己，感谢你们就点个赞点个关注哇，哈哈！！