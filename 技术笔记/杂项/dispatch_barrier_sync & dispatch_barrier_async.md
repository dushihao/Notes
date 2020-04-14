区别在于sync与async，是否是立即返回。

dispath_barrier_sync 官方文档说明：

> Submits a barrier block to a dispatch queue for synchronous execution. Unlike [`dispatch_barrier_async`](apple-reference-documentation://hcNWDuWN4P), this function does not return until the barrier block has finished. Calling this function and targeting the current queue results in deadlock.
>
> When the barrier block reaches the front of a private concurrent queue, it is not executed immediately. Instead, the queue waits until its currently executing blocks finish executing. At that point, the queue executes the barrier block by itself. Any blocks submitted after the barrier block are not executed until the barrier block completes.
>
> The queue you specify should be a concurrent queue that you create yourself using the [`dispatch_queue_create`](apple-reference-documentation://hccjgwsoUD) function. If the queue you pass to this function is a serial queue or one of the global concurrent queues, this function behaves like the [`dispatch_sync`](apple-reference-documentation://hci70qjHON) function.
>
> Unlike with `dispatch_barrier_async`, no retain is performed on the target queue. Because calls to this function are synchronous, it "borrows" the reference of the caller. Moreover, no `Block_copy` is performed on the block.
>
> As an optimization, this function invokes the barrier block on the current thread when possible.

这个函数直到 barrier block 已经结束才会返回，所以 barrier block 屏障之后追加不管是否在队列中的任务都会在之后追加和执行。



dispath_barrier_async 说明：

> Calls to this function always return immediately after the block has been submitted and never wait for the block to be invoked. When the barrier block reaches the front of a private concurrent queue, it is not executed immediately. Instead, the queue waits until its currently executing blocks finish executing. At that point, the barrier block executes by itself. Any blocks submitted after the barrier block are not executed until the barrier block completes. 
>
> The queue you specify should be a concurrent queue that you create yourself using the [`dispatch_queue_create`](apple-reference-documentation://hccjgwsoUD) function. If the queue you pass to this function is a serial queue or one of the global concurrent queues, this function behaves like the [`dispatch_async`](apple-reference-documentation://hcVyOLxquW) function.

Block 被提交后，调用这个`function` 总会立即返回并且不用等待这个块被执行。



**共同点** 

都是会将先执行 `barrier` 函数前，追加到 `concurrent queue` 的任务，然后在执行barrier函数中的任务，然后再执行之后追加到`concurrent queue`中的任务

**不同点**

`dispath_barrier_async` 由于是异步，不会影响其他队列（或者说线程？）的任务执行



#### 参考

[dispatch_barrier_async和dispatch_barrier_sync的使用](<https://blog.csdn.net/willluckysmile/article/details/61195571>)

