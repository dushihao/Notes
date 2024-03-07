### OC 内存管理、runtime、runloop、GCD、Block

1、引用计数存储在什么位置？

2、说明一下runloop里面的运行逻辑；runloop 和 autoreleasepool 之间的关系（也可以说runloop对于autoreleasepool实现的逻辑）；

3、block中 _forwwardding 指针的引入是为了解决什么问题？

​	Q：`__block`变量用结构体成员变量 `__forwarding`可以实现无论 `__block ` 变量配置在堆上还是栈上都能够正确的访问 `__block` 变量 (《Objective-C高级编程》书中有进行详细说明)



### Swift

static class 修饰的区别