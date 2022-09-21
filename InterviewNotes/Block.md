1. __block 关键字为什么可以修改捕获的局部变量的值

   简单点说：普通变量捕获的是值，`__block` 变量捕获的是指针。`__block`  说明符更准确的说是 “`__block` 存储域类说明符” ， `__block` 变量底层会被转化成 `__block_byref_val_0 `结构体，局部变量会成为该结构体的成员变量。Block的结构体实例持有 `__block_byref_val_0` 实例的指针，而 `__block_byref_val_0`  的成员变量`__forwarding`持有指向该实例自身的指针，通过 `__forwarding` 访问成员变量 `val` 。

   具体可参考《Objective-C高级编程iOS与OS X多线程和内存管理》。

   