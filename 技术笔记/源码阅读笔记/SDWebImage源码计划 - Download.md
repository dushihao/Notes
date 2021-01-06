# SDWebImage - Download

[TOC]

Version 5.7.4

参考链接：

- [浅析 SDWebImage 5.6](https://looseyi.github.io/post/sourcecode-ios/source-code-sdweb-1/)

#### 下载设计

```objective-c
@property (strong, nonatomic, nonnull) NSOperationQueue *downloadQueue;
```

可以通过`maxConcurrentOperationCount`设置最大并发数



网络回调的控制是通过 `task` 初始化一个session：

```objective-c
        /**
         *  Create the session for this task
         *  We send nil as delegate queue so that the session creates a serial operation queue for performing all delegate
         *  method calls and completion handler calls.
         */
        _session = [NSURLSession sessionWithConfiguration:sessionConfiguration
                                                 delegate:self
                                            delegateQueue:nil];
```

而关于delegateQueue传nil，session 会创建一个串行操作队列，以执行所有委托方法和完成处理程序调用，官方文档中有说明：

> ```
> queue
> ```
>
> An operation queue for scheduling the delegate calls and completion handlers. The queue should be a serial queue, in order to ensure the correct ordering of callbacks. If `nil`, the session creates a serial operation queue for performing all delegate method calls and completion handler calls.

这样就可以做到并发请求，在串行队列里进行代理回调处理任务。



#### 下载操作

通过自定义的  `NSOperation`  子类  **SDWebImageDownloaderOperation**   且遵守 `SDWebImageDownloaderOperation` 协议实现。

重写了几个关键方法，可以更加灵活管理下载状态：

```objective-c
- start;
- cancel;
- setFinished;
- setExecuting;
```



`SDWebImageDownloader`  实现了  `NSURLSessionDataDelegate` `NSURLSessionTaskDelegate` , 而真正的数据处理交给了 ``SDWebImageDownloaderOperation.m`` 

`SDWebImageDownloaderOperation.m` ：

```
#pragma mark NSURLSessionDataDelegate

- (void)URLSession:(NSURLSession *)session
          dataTask:(NSURLSessionDataTask *)dataTask
didReceiveResponse:(NSURLResponse *)response
 completionHandler:(void (^)(NSURLSessionResponseDisposition disposition))completionHandler {...}

- (void)URLSession:(NSURLSession *)session dataTask:(NSURLSessionDataTask *)dataTask didReceiveData:(NSData *)data {...}

- (void)URLSession:(NSURLSession *)session
          dataTask:(NSURLSessionDataTask *)dataTask
 willCacheResponse:(NSCachedURLResponse *)proposedResponse
 completionHandler:(void (^)(NSCachedURLResponse *cachedResponse))completionHandler {...}

#pragma mark NSURLSessionTaskDelegate

- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error {...}

- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didReceiveChallenge:(NSURLAuthenticationChallenge *)challenge completionHandler:(void (^)(NSURLSessionAuthChallengeDisposition disposition, NSURLCredential *credential))completionHandler {...}

- (void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didFinishCollectingMetrics:(NSURLSessionTaskMetrics *)metrics API_AVAILABLE(macosx(10.12), ios(10.0), watchos(3.0), tvos(10.0)) {...}
```



##### tips

1、有一处宏可以借鉴，用来判断当前队列是否是主队列

```objective-c
#ifndef dispatch_main_async_safe
#define dispatch_main_async_safe(block)\
    if (dispatch_queue_get_label(DISPATCH_CURRENT_QUEUE_LABEL) == dispatch_queue_get_label(dispatch_get_main_queue())) {\
        block();\
    } else {\
        dispatch_async(dispatch_get_main_queue(), block);\
    }
#endif
```

关于判断主线程主队列的讨论，可以参考 [GCD's Main Queue vs. Main Thread](http://blog.benjamin-encz.de/post/main-queue-vs-main-thread/)。

2、各种类型都进行了协议化。





