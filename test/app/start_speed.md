

#### 启动分类

##### 冷启动

Click event ipc process.start activity Thread Android单独进程的入口类 bind application ->activity life cycle

Viewrootimpl 页面绘制

系统行为：

​	启动APP 创建application 启动主线程  创建activity 

##### 热启动

后台->前台

##### 温启动

重走activity的生命周期 life cycle 

#### 优化方向

#### 启动时间测量

##### adb 命令

``````shell
adb shell am start -W 包名/activity
ThisTime 最后一个activity启动需要时间
TotalTime 所有activity启动需要时间，，是否有Splash界面
WaitTime AMS(ActivityManagerService)启动activity的总耗时
ThisTime < TotalTime < WaitTime
线下使用方便，可以查看竞品时间，不严谨、精确
``````

##### 手动打点

* 误区

``````
onWindowsFocusChanged只是首帧时间，activity首次绘制时间w
``````

* 正解

  feed第一条展示的时间

  

#### 优化工具

##### 优势

* 图形化的展示执行时间，调用栈等
* 信息全面、包含所有信息
* 可以在代码中埋点，用cpu profile分析

##### 缺点

* 运行时开销大，整体都会变慢很多，因为要抓取所有线程的调用信息

* 因为运行消耗大，所以可能会带偏优化方向

  

#### Systrace

#### 获取耗时的方法

