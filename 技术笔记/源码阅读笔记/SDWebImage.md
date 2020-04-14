### 参考

[SDWebImage实现分析](<http://southpeak.github.io/2015/02/07/sourcecode-sdwebimage/>)

[源码浅析 SDWebImage 5.5.2](<https://juejin.im/post/5e63d5a9f265da5729789ac3#heading-11>)

### 阅读笔记

1、多线程安全

5.x版本，统一使用信号量控制

```objective-c
#ifndef SD_LOCK
#define SD_LOCK(lock) dispatch_semaphore_wait(lock, DISPATCH_TIME_FOREVER);
#endif

#ifndef SD_UNLOCK
#define SD_UNLOCK(lock) dispatch_semaphore_signal(lock);
#endif
```



3.x版本，在处理回调信息时，使用的 `` dispatch_barrier_async(dispatch_queue_t queue, dispatch_block_t block)`` 

