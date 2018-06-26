# 并发编程

##### 1、GCD 的一些常用的函数？（group，barrier，信号量，线程同步）

###### group

我们使用队列组来开辟线程时，队列组中的队列任务是并发，当所有的队列组中的所有任务完成时候，才可以调用队列组完成任务。

```
/**创建自己的队列*/
dispatch_queue_t dispatchQueue = dispatch_queue_create("ted.queue.next", DISPATCH_QUEUE_CONCURRENT);
/**创建一个队列组*/
dispatch_group_t dispatchGroup = dispatch_group_create();
/**将队列任务添加到队列组中*/
dispatch_group_async(dispatchGroup, dispatchQueue, ^(){
    NSLog(@"dispatch-1");
});
   /**将队列任务添加到队列组中*/
dispatch_group_async(dispatchGroup, dispatchQueue, ^(){
    NSLog(@"dspatch-2");
});
  /**队列组完成调用函数*/
dispatch_group_notify(dispatchGroup, dispatch_get_main_queue(), ^(){
    NSLog(@"end");
})
```

###### barrier

表示栅栏，当在并发队列里面使用栅栏时候，栅栏之前的并发任务开始并发执行，执行完毕后，执行栅栏内的任务，等栅栏任务执行完毕后，再并发执行栅栏后的任务。

```
dispatch_queue_t concurrentQueue = dispatch_queue_create("my.concurrent.queue", DISPATCH_QUEUE_CONCURRENT);
dispatch_async(concurrentQueue, ^(){
    NSLog(@"dispatch-1");
});
dispatch_async(concurrentQueue, ^(){
    NSLog(@"dispatch-2");
});
dispatch_barrier_async(concurrentQueue, ^(){
    NSLog(@"dispatch-barrier"); 
});
dispatch_async(concurrentQueue, ^(){
    NSLog(@"dispatch-3");
});
dispatch_async(concurrentQueue, ^(){
    NSLog(@"dispatch-4");
});
```

###### 信号量

Semaphore是通过‘计数’的方式来标识线程是否是等待或继续执行的。

[信号量](https://www.jianshu.com/p/04ca5470f212)

```
dispatch_semaphore_create(int) // 创建一个信号，并初始化信号的计数大小
/* 等待信号，并且判断信号量，如果信号量计数大于等于你创建时候的信号量的计数，就可以通过，继续执行，并且将你传入的信号计数减1，
 * 如果传入的信号计数小于你创建的计数，就表示等待，等待信号计数的变化
 *  如果等待的时间超过你传入的时间，也会继续下面操作
 *   第一个参数：semaphore 表示信号量
 *   第二个参数：表示等待的时间
 *    返回int 如果传入的信号计数大于等于你创建信号的计数时候，返回0.  反之，返回的不等于0
 */
 int result = dispatch_semaphore_wait(dispatch_semaphore_t  semaphore,time outTime);// 表示等待，也是阻碍线程  
// 表示将信号技术+1
dispatch_semaphore_signl(dispatch_semaphore_t semaphore);
```

```
- (void)semaphoreDemo1 {
    dispatch_semaphore_t semaphore = dispatch_semaphore_create(10);
    for (int i = 0; i < 100; i++) {
        //// 由于是异步执行的，所以每次循环Block里面的dispatch_semaphore_signal根本还没有执行就会执行dispatch_semaphore_wait，从而semaphore-1.当循环10此后，semaphore等于0，则会阻塞线程，直到执行了Block的dispatch_semaphore_signal 才会继续执行
        NSLog(@"i %zd",i);
        /// 执行十次之后阻塞当前线程
        dispatch_semaphore_wait(semaphore, DISPATCH_TIME_FOREVER);
    }
}

- (void)semaphoreDemo2 {
    dispatch_semaphore_t goOnSemaphore = dispatch_semaphore_create(0);
    NSLog(@"ready");
    [self network:^(id result) {
        NSLog(@"net return:%@",result);
        dispatch_semaphore_signal(goOnSemaphore);
    }];
    dispatch_semaphore_wait(goOnSemaphore, DISPATCH_TIME_FOREVER);
    NSLog(@"go on");
}
- (void)network:(void(^)(id result))block {
    sleep(2.0);
    block(@(arc4random_uniform(2)));
}
```

###### 线程同步

实现线程同步的方法：串行队列，分组，信号量

也可以使用并发队列：

```
//加入队列
dispatch_async(concurrentQueue, ^{
    //1.先去网上下载图片
    dispatch_sync(concurrentQueue, ^{

    });
    //2.在主线程展示到界面里
    dispatch_sync(dispatch_get_main_queue(), ^{

    });
});
```

##### 2、如何使用队列来避免资源抢夺？

```
使用线程锁。也是可以使用串行队列来完成。如：fmdb就是使用FMDatabaseQueue，来解决多线程抢夺资源。
```





