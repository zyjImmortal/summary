\1. 什么叫压力补偿，压力补偿的作用是什么？ 2. 还有为什么要动态扩展？ 比如内存不够了，我们不应该找到谁占用了内存吗？ 3.每次测试前需要清理缓存吗？比如我跑一轮脚本 就需要把redis 缓存清一下吗 ？

\1. 就是在场景执行的过程中，发现场景产生的压力和生产上比例不一致，或者有中间需要的增加的第三方压力。就需要在场景执行过程中再增加新的线程或者压力机。

\2. 动态扩展是验证线上的能力。如果业务流量突增了。就需要动态扩展哇。

\3. 看机制。如果不是预热类型的。 可以在每次跑之前清一下