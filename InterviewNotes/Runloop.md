

[RunLoop 的概念](https://blog.ibireme.com/2015/05/18/runloop/#base)



在 Core Foundation 框架中，CFRunLoopSourceRef 是 RunLoop 事件源的抽象对象，在使用时需要指定该事件源的类型，分为 kCFRunLoopSourceTypeCustom 和 kCFRunLoopSourceTypeMachPort 两种类型。

Source分为两种：

Source0：非基于Port的 用于用户主动触发的事件（点击button 或点击屏幕）<br>
Source1：基于Port的 通过内核和其他线程相互发送消息（与内核相关）<br>
注意：Source1在处理的时候会分发一些操作给Source0去处理，例如 UIButton点击事件（可以查看上方链接评论区的讨论）



RunLoop主要有以下几个组成部分：

1. Input Source（输入源）：输入源是RunLoop的事件来源，例如触摸事件、网络请求、定时器等。当有事件发生时，RunLoop会从输入源中获取事件并进行处理。
2. Mode（运行模式）：运行模式是RunLoop的运行状态，它会影响RunLoop处理事件的方式。常见的运行模式有Default、Common、Tracking等。
3. Observer（观察者）：观察者是RunLoop的监控机制，它可以在RunLoop执行特定的事件时进行观察和处理。观察者可以用来监控RunLoop的状态、处理事件的时间等。 RunLoop的主要作用是优化应用程序的性能和响应速度。它可以在等待事件时将线程置于休眠状态，从而节省CPU资源；同时它也可以在有事件到来时及时地将线程唤醒，从而提高应用程序的响应速度。RunLoop还可以保护线程安全，防止多个线程同时访问同一份数据，从而避免了数据竞争和死锁的问题。 在iOS应用程序开发中，RunLoop常常用于以下几个方面：
4. 处理UI事件：RunLoop负责处理用户界面的事件，例如触摸事件、按钮点击事件等。
5. 定时器：RunLoop可以管理定时器的执行，例如在后台执行一些周期性的任务。
6. 网络请求：RunLoop可以管理网络请求的执行，例如在后台执行一些需要网络请求的任务。
7. 多线程：RunLoop可以协调和管理多个线程之间的执行，例如在多线程环境下保证线程安全性。 总之，RunLoop是iOS系统中一个非常重要的基础框架，它的作用在于协调和管理应用程序中的事件和线程，从而提高应用程序的性能和响应速度。



Q: UIButton 触摸事件



