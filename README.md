# QAIO：兼顾性能与抽象的现代C++网络库

## 为什么需要QAIO？

``` 
传统socket编程的痛点之一是缺乏对需求语境的建模。
```
需求视角：A和B之间要有“连接”。

实现视角：A和B之间新建连接x，要是x断了，得赶紧重建一条连接y保证A、B恢复通信。

需求语境下“连接”是对AB之间能够持续通信这一目标状态的抽象;

实现语境下的“连接”则是对tcp, ipc等具体底层执行序列的抽象。

如果编程者只面向需求语境的“连接”编程，毫无疑问会轻松很多。

```
传统socket编程的痛点之二是糟糕的系统调用接口。
```

相当一部分posix API非常啰嗦麻烦丑陋反直觉反人类，眼不见为净，还是将它们通通封装成现代、简洁、一致的接口更让人舒心。

```
和其他MQ、基于事件的网络库比有何创新？
```

1. 并发的实现依赖intel tbb（非常优秀的多核算法支撑）。nng、libzmq、muduo（好吧我只读过这三个）等网络库只是简单使用pthread，性能上不如tbb的task抽象，此外原生malloc和new也远不如tbb多核算法下的内存分配。

2. 更加灵活、轻量。qaio让用户可以面向需求语境编程，不必担心连接管理、重试、心跳等细节，但严格地止步于此。用户需要自行实现通信协议。网络库这种多线程基础设施非常容易出现bug或让用户困惑的隐式约束，规模越大越危险。qaio浅浅一层很容易读透，没有注入太多隐式约束，让用户对自己的项目有完全掌控。

3. 最小化的抽象。这个库的用户是强横的c++网络程序员，绝非需要呵护的存在，给他们渔好过鱼。很多时候他们甚至只需要你的库中某个组件，因为他们的业务通常也是造轮子。过分的抽象反而造成性能上难以优化或功能上不好调整的局限。

## QAIO的核心特性

``` 
有名有姓的智能socket
```
1. 它是进程内对象，生命周期受所在进程控制。
2. 它有姓氏：ip:port。
3. 它有名字：当前进程启动日期 | 当前进程pid。
4. 假如它姓A，就会收到所有发给A的消息，哪怕是发给它的父辈的，它可以考虑抛弃这些消息，也可以选择处理。
5. 它与别的智能socket刚刚建立连接时会交换名字。连接建立后这些socket就叫做peers。
6. 它发送消息给peer时，就以peer的姓为地址，名为附加信息。
7. 它发送的消息必然完整抵达，如果没办法完整地发出去，就一个字节都不会发。
8. 支持匿名socket，适合那些不需要监听某个端口的客户端程序。



```

```




