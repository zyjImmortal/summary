#### 崩溃收集

##### 崩溃信息

* 进程名、线程名，崩溃的进程是前台进程还是后台进程，是不是发生在UI线程
* 崩溃堆栈和类型，崩溃是属于java崩溃还是native崩溃，还是ANR，这里要着重查看崩溃堆栈的栈顶，是我们写的代码还是系统的代码

##### 系统信息

* logcat

应用和系统的运行日志，由于系统权限问题，获取到的 Logcat 可能只包含与当前 App 相关的。其中系统的 event logcat 会记录 App 运行的一些基本情况，记录在文件 /system/etc/event-log-tags 中



* 机型、设备、厂商、CPU、系统版本等
* 设备状态：是否root、是否模拟器，Xposed

##### 内存信息

OOM、ANR、虚拟内存耗尽等，很多崩溃都跟内存有直接关系，手机内存越小崩溃率越高

* 系统剩余内存，可以从系统读取文件 /proc/meminfo，这个文件里有当前内存使用的相关信息，如果系统可用内存很小（低于 MemTotal 的 10%）时，OOM、大量 GC、系统频繁自杀拉起等问题都非常容易出现
* 应用使用内存，包括 Java 内存、RSS（Resident Set Size）、PSS（Proportional Set Size），可以得出应用本身内存的占用大小和分布
* 虚拟内存，虚拟内存可以通过/proc/self/status 得到，通过 /proc/self/maps 文件可以得到具体的分布情况

##### 资源信息

有的时候我们会发现应用堆内存和设备内存都非常充足，还是会出现内存分配失败的情况，这跟资源泄漏可能有比较大的关系

* 文件句柄 fd，文件句柄的限制可以通过 /proc/self/limits 获得，一般单个进程允许打开的最大文件句柄个数为 1024。但是如果文件句柄超过 800 个就比较危险，需要将所有的 fd 以及对应的文件名输出到日志中，进一步排查是否出现了有文件或者线程的泄漏

![image-20200929105819142](https://zyjblog.oss-cn-beijing.aliyuncs.com/1601348342.png)

