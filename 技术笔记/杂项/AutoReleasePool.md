### 学习链接

[黑幕背后的Autorelease](<https://blog.sunnyxx.com/2014/10/15/behind-autorelease/>)

**系统在每个runloop迭代中都加入了自动释放池Push和Pop**

### 原理

##### AutoreleasePoolPage

C++ 实现类，双向链表，哨兵对象，next指针

##### objc_autoreleasePoolPush & objc_autoreleasePoolPop

