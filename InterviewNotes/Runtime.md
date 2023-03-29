Q如何hook一个对象的方法，而不影响其它对象？

A：利用KVO实现原理进行实现。

Q：消息转发流程

A：

```objective-c
// 第一阶段：提供一个函数实现，返回NO则进入下个阶段
+ (BOOL)resolveInstanceMethod:(SEL)sel;
+ (BOOL)resolveClassMethod:(SEL)sel;
// 第二阶段：替换消息的接受者，如果返回nil或者self，则进入消息转发机制
- (id)forwardingTargetForSelector:(SEL)aSelector;
// 进入完整消息转发流程
- (void)forwardInvocation:(NSInvocation *)anInvocation;
- (NSMethodSignature *)methodSignatureForSelector:(SEL)aSelector;
```



Q: 分类为何不能添加属性

A: 在编译器已经确定了内存的布局，分类却是在运行时加载的。即使声明了属性，并不会生成实例变量、setter、getter方法。

Q: 分类如何添加弱引用属性

A: [Associated Object 介绍](https://sunsetroads.github.io/2020/03/22/weak-associated-object/)

在关联对象中通过中间层__weak关键字和block、或者用一个包装类去实现。
