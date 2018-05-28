# SDWebImage

SDWebImage 提供一个 UIImageView 的分类以支持加载远程图片，具有缓存管理、异步下载、同一个 URL 下载次数控制和优化等特点。

### 工作流程

1. 入口 `setImageWithURL:placeholderImage:options` 会先把 `placeholderImage` 显示，然后 `SDWebImageManger` 根据 `URL` 开始处理图片。
2. 进入 `SDWebImageManager-downloadWithURL:delegate:options:userInfo:`, 交给 `SDImageCache` 从缓存查找图片是否已经下载 `queryDiskCacheForKey:delegate:userInfo:`.
3. 先从内存图片缓存查找是否有图片，如果内存中已经有图片缓存，`SDImageCacheDelegate` 回调 `imageCache:didFindImage:forKey:userInfo:`到 `SDWebImageManager`。
4. `SDWebImageManagerDelegate` 回调 `webImageManager:didFinishWithImage:`到 `UIImageView+WebCache` 等前端展示图片。
5. 如果内存缓存中没有，生成 `NSInvocationOperation` 添加到队列开始从硬盘查找图片是否已经缓存。
6. 根据 `URLKey` 在硬盘缓存目录下尝试读取图片文件。这一步是在 `NSOperation` 进行的操作，所以会主线程进行结果回调 `notifyDelegate:`。
7. 如果上一个操作从硬盘读取到了图片，将图片添加到内存缓存中（如果空闲缓存过小，会先清空内存缓存）。`SDImageCacheDelegate` 回调 `imageCache:didFindImage:forKey:userInfo:`。进行回调展示图片。
8. 如果从硬盘缓存目录读取不到图片，说明所有缓存都不存在该图片，需要下载图片，回调`imageCache:didNotFindImageForKey:userInfo:`。
9. 共享或重新生成一个下载器 `SDWebImageDownloader` 开始下载图片。
10. 图片下载由 NSURLConnection 来做，实现相关 delegate 来判断图片下载中、下载完成和下载失败。
11. `connection:didReceiveData:` 中利用 `ImageIO` 做了按图片下载进度加载效果。`connectionDidFinishLoading:`数据下载完成后交给 `SDWebImageDecoder` 做图片解码处理。
12. 图片解码处理在一个 `NSOperationQueue` 完成，不会拖慢主线程 UI。如果需要对下载的图片做二次处理，最好也在这里完成，效率会好很多。
13. 在主线程 `notifyDelegateOnMainThreadWithInfo:`宣告解码完成，`imageDecoder:didFinishDecodingImage:userInfo:`回调给 `SDWebImageDownloader`。`imageDownloader:didFinishWithImage:`回调给 `SDWebImageManager` 告知图片下载完成。
14. 通知所有的 `downloadDelegates` 下载完成，回调给需要的地方展示图片。将图片保存到 `SDImageCache` 中，内存缓存和硬盘缓存同时保存 。写文件到磁盘也在单独的 `NSInvocationOperation` 完成，避免拖慢主线程。
15. `SDImageCache` 在初始化的时候会注册一些消息通知，在内存警告或退到后台的时候清理内存图片缓存，应用结束的时候清理过期图片。
16. `SDWebImagePrefetcher` 可以预先下载图片，方便后续使用。

### 源码分析

##### 图片下载

###### SDWebImageDownloader

1. 单例，图片下载器，负责图片异步下载
2. 图片的下载操作放在一个 NSOperationQueue 并发操作队列中，队列默认最大并发数是 6
3. 每个图片对应一些回调（下载进度，完成回调等），回调信息会保存在 downloader 的 URLCallbacks 中（一个字典，key 是 url 地址，value 是图片下载回调数组），URLCallbacks 可能被多个线程访问，所以 downloader 把下载任务放在一个 barrierQueue 中，并设置屏障保证同一时间只有一个线程访问 URLCallbacks。在创建回调 URLCallbacks 的 block 中创建一个 NSOperation 并添加到 NSOperationQueue 中。
4. 每个图片下载都是一个 operation，创建后添加到一个操作队列中，SDWebImage 定义了一个协议 SDWebImageOperation 作为图片下载操作的基础协议，声明了一个 cancel 方法，用于取消操作。

   ```
   @protocol SDWebImageOperation <NSObject>
   - (void)cancel;
   @end
   ```

5. 对于图片的下载，SDWebImageDownloaderOperation 完全依赖于 NSURLConnection 类，实现了 NSURLConnectionDataDelegate 协议的方法来跟踪图片下载进度。

###### SDWebImageDownloaderOperation

1. 继承自 NSOperation 类，没有简单实现 main 方法，而是采用更加灵活的 start 方法，以便自己管理下载的状态。
2. start 方法中创建了下载使用的 NSURLConnection 对象，开启了图片的下载，并抛出一个下载开始的通知。
3. 下载的核心是利用 NSURLSession 加载数据，每个图片下载都有一个 operation 操作来完成，并将这些操作放到一个操作队列中，这样可以实现图片的并发下载。

###### SDWebImageDecoder

（异步对图片进行解码）

##### 缓存

下载完图片后缓存到本地，再获取同一张图片时，直接从本地获取，减少网络流量，提升用户体验，能快速从本地获取呈现给用户。

SDWebImage 提供了对图片进行缓存，主要由 SDImageCache 完成。该类负责处理内存缓存以及一个可选的磁盘缓存，其中磁盘缓存的写操作是异步的，不会对 UI 造成影响。

1. 内存缓存及磁盘缓存
   1. 内存缓存的处理由 NSCache 对象实现，NSCache 类似与 NSDictionary，存储 key-value 对，通常使用缓存类临时存储短时间使用但创建昂贵的对象，重用这些对象可以优化性能，同时这些对象对于内存来说不是紧要的，如果内存紧张就会自动释放。
   2. 磁盘缓存的处理使用 NSFileManager 对象实现，图片存储的位置位于 cache 文件夹，另外 SDImageCache 还定义了一个串行队列来处理存储图片。
   3. SDImageCache 提供了大量方法来缓存、获取、移除及清空图片。对于图片的索引，我们通过一个 key 来索引，在内存中，将其作为 NSCache 的 key 值，在磁盘中，用这个 key 值作为图片的文件名，使用 url 作为 key。
2. 存储图片
   现在内存中放置一份缓存，如果需要缓存到磁盘，将磁盘缓存操作作为一个 task 放到 串行队列中处理，会先检查图片格式，将其转换为相应的图片数据，最后把数据写入磁盘中（文件名是对 key 值做 MD5 后的串）。
3. 查询图片  
   内存和磁盘查询图片 API：

   ```
   - (UIImage *)imageFromMemoryCacheForKey:(NSString *)key;
   - (UIImage *)imageFromDiskCacheForKey:(NSString *)key;
   ```

   查看本地是否存在 key 指定的图片，API：

   ```
   - (NSOperation *)queryDiskCacheForKey:(NSString *)key done:(SDWebImageQueryCompleteBlock)doneBlock;
   ```

4. 移除图片  
   移除图片 API：

   ```
   - (void)removeImageForKey:(NSString *)key;
   - (void)removeImageForKey:(NSString *)key withCompletion:(SDWebImageNoParamsBlock)completion;
   - (void)removeImageForKey:(NSString *)key fromDisk:(BOOL)fromDisk;
   - (void)removeImageForKey:(NSString *)key fromDisk:(BOOL)fromDisk withCompletion:(SDWebImageNoParamsBlock)completion;
   ```

5. 清理图片（磁盘）  
   清空磁盘图片可以选择完全情况和部分清空，部分清空就是把缓存文件夹删除

   ```
   - (void)clearDisk;
   - (void)clearDiskOnCompletion:(SDWebImageNoParamsBlock)completion;
   ```

   部分清理 会根据设置的一些参数移除部分文件，主要有两个指标：文件的缓存有效期（maxCacheAge: 默认是一周）和最大缓存空间大小（maxCacheSize: ）

##### 小结

SDImageCache 除了提供以上 API，还提供了获取缓存大小，缓存中图片数据等 API。

##### SDWebImageManger

实际使用中并不直接使用SDWebImageDownloader和SDImageCache类对图片进行下载和存储，而是使用SDWebImageManager来管理。包括平常使用UIImageView+WebCache等控件的分类，都是使用SDWebImageManager来处理，该对象内部定义了一个图片下载器（SDWebImageDownloader）和图片缓存（SDImageCache）。

##### 视图扩展

### 技术点

1、dispatch\_barrier\_sync函数，用于对操作设置顺序，确保在执行完任务后再确保后续操作。常用于确保线程安全性操作。

2、NSMutableURLRequest：用于创建一个网络请求对象，可以根据需要来配置请求报头等信息

3、NSOperation及NSOperationQueue：操作队列是OC中一种告诫的并发处理方法，基于GCD实现，相对于GCD来说，操作队列的优点是可以取消在任务处理队列中的任务，另外在管理操作间的依赖关系方面容易一些，对SDWebImage中我们看到如何使用依赖将下载顺序设置成后进先出的顺序

4、NSURLSession：用于网络请求及相应处理

5、NSCache类：一个类似于集合的容器，存储key-value对，这一点类似于nsdictionary类，我们通常用使用缓存来临时存储短时间使用但创建昂贵的对象。重用这些对象可以优化性能，因为它们的值不需要重新计算。另外一方面，这些对象对于程序来说不是紧要的，在内存紧张时会被丢弃

6、清理缓存图片的策略：特别是最大缓存空间大小的设置。如果所有缓存文件的总大小超过这一大小，则会按照文件最后修改时间的逆序，以每次一半的递归来移除那些过早的文件，直到缓存的实际大小小于我们设置的最大使用空间。

7、图片解压操作：这一操作可以查看SDWebImageDecoder.m中+decodedImageWithImage方法的实现。



