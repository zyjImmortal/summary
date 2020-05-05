不知道大家有没有这种需求，就是先在本地写好博客文章，然后再发到自己的博客平台上，或者通过[OpenWrite](https://openwrite.cn/)这样的分发工具，将文章一键转发到各个平台。但是这其中就有个问题，之前小编在本地写都是用的Typora这款Markdown编辑器写的，每次写完还得到平台上再把图片手动一张一张传上去，贼费劲，所以就想着Typora能不能帮我实现粘贴后自动上传，网上搜了搜，果然有，下面来看看，具体怎么操作。

**重要：一定要将Typora更新到最新版本**

## 选择上传工具

偏好设置->图像->顶部的下拉框选择上传图片，下面会出现Typora支持图片上传方式

![image-20200505103213822](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588645934.png)



这里我选择的是uPic这款工具，Ipic收费，其他的工具我没去试，大家有兴趣的可以尝试一下。

## uPic安装

上面选择uPic工具后，会提示你安装这个程序，点击会跳转到[下载地址](https://blog.svend.cc/upic/),有两种安装方式，直接下载dmg安装包和`brew cask install upic`,这里我先试了dmg安装方式，安装后启动的时候，mac会提示不安全，这个时候需要做一些特殊的处理才能让mac信任它，果断卸载直接使用命令行安装，成功后，启动时不成问题的。



## uPic配置

我使用的阿里云的图床，说这里只说阿里云的配置方式，其他的可以去[这里看](https://blog.svend.cc/upic/).

![image-20200505104405663](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588646645.png)

打开uPic的偏好设置,选择图床,点击左下角的+号,选择阿里云,就会出先上面的配置界面.

* 区域

就是购买的oss服务在哪个区域，一般在bucket列表的右上角就能看到

* 空间名称

就是bucket，填入自己新建好的bucket

* Access key

阿里云的AccessKey ID

* Secret key

阿里云的Access Key Secret

* 域名

![image-20200505105109229](https://zyjblog.oss-cn-beijing.aliyuncs.com/1588647069.png)

每个bucket的详情页都会有这个，获取到bucket的外网访问域名，注意在填入uPic的时候要加上http://或https://

* 保存路径

保存路径根据自己的习惯定义一个就好，我使用的{since_second}{.suffix},如果设置成uPic/{since_second}{.suffix},会自动在阿里云上给新建一个文件夹，把文件都存放这个文件夹下面

接下来再Typora体验一下，截图直接复制到剪贴板，然后复制到Typora，就能自动上传了

