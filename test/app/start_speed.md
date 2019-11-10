

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
线下使用方便，可以查看竞品时间，不严谨、不精确，也不能够带到线上
``````

##### 手动打点

* 误区

``````
onWindowsFocusChanged只是首帧时间，activity首次绘制时间w
``````

* 正解

  feed第一条展示的时间


### 优化工具

#### traceview

##### 优势

* 图形化的展示执行时间，调用栈等
* 信息全面、包含所有信息
* 可以在代码中埋点，用cpu profile分析

##### 缺点

* 运行时开销大，整体都会变慢很多，因为要抓取所有线程的调用信息

* 因为运行消耗大，所以可能会带偏优化方向


##### 使用方式

``````java
Debug.startMethodTracing();
Debug.stopMethodTracing();
APP目录的file文件夹下
``````

- Call Chart 
- Flame Chart
- Top Down
  - total 总的时间=self+children
  - self  函数自己执行时间
  - Children 函数中调用其他函数的时间
  - Thread Time  线程Cpu的执行时间，真正在执行代码的时间
  - Wall Clock Time 线程执行的所有时间包括sleep时间，程序执行时间
  - 橙色 系统
  - 应用 绿色
  - 第三方 蓝色
- systrace
  - b buffersize
  - t 时间
  - a 包名
  - o 生成文件名
  - 选中tag点击m键
  - 轻量级、开销小，只抓埋的地方
  - 直观反映cpu利用率
    - wall duration time 代码执行时间，包含cputime和等待其他指令或者调用其他函数的返回值
    - cpu duration 是代码消耗cpu 的时间（重点指标）
- Bottom Up

#### Systrace

#### 获取耗时的方法

aop：面向切面编程，针对同一类问题统一处理，对代码是无侵入的

JointCut连接点：程序运行时的执行点，可以作为切面的地方

PointCut：带条件的JointCut，是有条件的JointCut，我们并不是想在左右的JointCut上执行advice，比如所有的方法前后都可以作为JointCut，但是我们只想对特定的方法计算耗时，那这些特定的方法前后就是PointCut

切面（Aspect）：切面是通知和切入点的结合，就是写个类用@Aspect标记，然后在定义advice和切点

切点表达式：定义匹配方法的规则

Advice：一种hook，就是你想要的功能，比如日志、耗时计算等

- Before，在PointCut之前执行
- After，在之后执行
- Around，前后都执行



### 启动优化

- theme切换，只是感觉上的快，
- 子线程分担主线程的任务，减少并行时间,启动的时候不要new线程，没有复用的概念，所以要使用线程池

```java
ExecutorService service = Executors.newFixedThreadPool(CORE_POOL_SIZE);

service.submit(new Runnable() {
    @Override
    public void run() {
        initBugly();
    }
});
```

- 异步优化注意的地方
  - 要注意有些代码必须要在主线程执行，这些代码是不能异步的
  - 异步的时候要考虑，主线程会不会用到子线程的的处理结果，如果需要可以使用CountDownLatch来处理
- 常规异步痛点
  - 代码不优雅
  - 场景不好处理(依赖关系)
  - 维护成本高
- 启动器
  - 核心思想：充分利用CPU的多核，自动梳理任务顺序
  - 流程
    - 代码task化，启动逻辑抽象为Task
    - 根据所有任务依赖关系排序生成一个有向无环图
    - 多线程按照排序后的优先级依次执行

