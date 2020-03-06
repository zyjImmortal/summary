![image-20200207141139476](/Users/zhouyajun/Library/Application Support/typora-user-images/image-20200207141139476.png)

websocket是一种在单个tcp连接上进行的全双工通信协议，在websocket API中，浏览器和服务器只需完成一次握手，这里的一次握手指的是，建立tcp连接后传输一次握手数据，两者之间就可以创建持久性的链接，并进行双向数据传输。

通过tcp建立连接，传递符合http协议的数据包

![image-20200207221618187](/Users/zhouyajun/Library/Application Support/typora-user-images/image-20200207221618187.png) 

![image-20200208220223463](/Users/zhouyajun/Library/Application Support/typora-user-images/image-20200208220223463.png)