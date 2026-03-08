### OC 内存管理、runtime、runloop、GCD、Block

###### 1、引用计数存储在什么位置？

参考：当引用计数超过`extra_rc`的容量时，系统会使用`side table`来存储额外的引用计数。`side table`是一个全局的哈希表，其键是对象的指针，值是引用计数。

###### 2、说明一下runloop里面的运行逻辑；runloop 和 autoreleasepool 之间的关系（也可以说runloop对于autoreleasepool实现的逻辑）；如果利用runloop检测卡顿

1 通知 observer 即将进入 loop

2 通知 observer 即将处理 timer

3 通知 observer 即将处理 source0

4 处理 source0

5 如果有source1 则跳转到9去处理source1

6 通知 observer 线程即将进入休眠

7 休眠等待唤醒

8 通知 observer 现成刚被唤醒

9 处理唤醒后收到的消息，之后跳回2

10 通知 observer 即将退出 loop

###### 3、block中 _forwwardding 指针的引入是为了解决什么问题？

​	Q：`__block`变量用结构体成员变量 `__forwarding`可以实现无论 `__block ` 变量配置在堆上还是栈上都能够正确的访问 `__block` 变量 (《Objective-C高级编程》书中有进行详细说明)



### Swift

static class 关键字的区别

| 特性           | `static`                     | `class`                    |
| :------------- | :--------------------------- | :------------------------- |
| **适用范围**   | 类、结构体、枚举             | 仅类                       |
| **是否可重写** | 不可重写                     | 可重写                     |
| **绑定方式**   | 静态绑定                     | 动态绑定                   |
| **使用场景**   | 不可重写的类型级别属性和方法 | 可重写的类型级别属性和方法 |

