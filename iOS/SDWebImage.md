SDWebImage[传送门](https://github.com/rs/SDWebImage)

1. 调用逻辑
(1) 
```
- (void)sd_setImageWithURL:(nullable NSURL *)url placeholderImage:(nullable UIImage *)placeholder
```
调用函数的入口

(2)
```
- (void)sd_setImageWithURL:(nullable NSURL *)url
          placeholderImage:(nullable UIImage *)placeholder
                   options:(SDWebImageOptions)options
                  progress:(nullable SDWebImageDownloaderProgressBlock)progressBlock
                 completed:(nullable SDExternalCompletionBlock)completedBlock
```
UIImageView的总的方法

(3)
```
- (void)sd_internalSetImageWithURL:(nullable NSURL *)url
                  placeholderImage:(nullable UIImage *)placeholder
                           options:(SDWebImageOptions)options
                      operationKey:(nullable NSString *)operationKey
                     setImageBlock:(nullable SDSetImageBlock)setImageBlock
                          progress:(nullable SDWebImageDownloaderProgressBlock)progressBlock
                         completed:(nullable SDExternalCompletionBlock)completedBlock
```
UIView内部的方法
1. 根据self的class生成字符串作为key
2. 取消self上图片的下载（这是为了防止tableView滚动导致先下载的图片慢，后下载的图片快，覆盖后下载的导致错乱）
3. 给UIView加一个属性，imageUrl,值为url
4. 位运算判断使用延迟Placeholder
5. 没有延迟的话，主线程调用setImageBlock，回调placeholderImage
6. 判断url空，空的话主线程移除indicator并返回错误
7. 有url，判断是否能够显示Indicator
8. 弱引用self
9. 通过SDWebImageManager，下载图片，返回一个operation
    10. 强引用weakSelf,并且下一行中判空，强引用为了防止多线程在运行期释放，判断为了防止nil导致不必要的错误
    11. 在回到主线程继续判空，理由同上
    12. 如果存在image,并且是禁止自动设置图片，并且存在completedBlock ，则回调
    13. 如果存在图片，则通过setImageBlock完成图片设置
    14. 如果不存在image,并且设置了延迟placeholder则设置placeholder图片
    15. 如果有成功的回调，并且finished调用工程回调
16. 设置这个operation
    17. 如果key存在，则根据key先取消UIView上图片下载operation
    18. 如果operation存在将operation,放到UIView关联的Dictionary中
    
(4)
```
- (id <SDWebImageOperation>)loadImageWithURL:(nullable NSURL *)url
                                     options:(SDWebImageOptions)options
                                    progress:(nullable SDWebImageDownloaderProgressBlock)progressBlock
                                   completed:(nullable SDInternalCompletionBlock)completedBlock
```
1. 下载了图片却又没有设置completedBlock，使用断言提示使用prefetchURLs
2. 判断url是否是NSString，如果是NSString转成NSURL，如果还不是NSURL，将url设置成nil
3. 新建一个SDWebImageCombinedOperation
4. 紧接着weak这个operation(不太明白为什么要这么做)
5. 判断是否是失败的url, 加锁并且从集合中查找
6. 如果url.absoluteString长度为0，或者并且不需要重试，并且是失败的url
7. 取消这个operation并返回错误
8. 将runningOperations加锁并加入这个operation
9. 获取url的cacheKey
10. 从imageCache中根据key获取cacheOperation
    11. 判断operation是否取消，取消则移除
    12. 如果没有cacheImage,或者options需要刷新缓存，并且代理响应了方法
        13. 如果有cachedImage ，并且refreshCached
        14. 先调用从cache中获取图片
        15. 一堆位枚举设置 downloaderOptions
        16. imageDownloader根据url下载图片并返回subOperationToken
            1. 图片下载回来之后，先判断强引用operation 
            2. 判断operation不存在，或者取消，不做任何操作
            3. 判断error
                1. 调用完成Operation
                2. 往failedURLs中添加url
            4. operation存在，并且没有取消，并且没有错误
                1. 判断是否重试
                2. 判断是否是否只存内存
                3. 刷新图片，并且有CacheImage但是没有下载图片，不做任何处理
                4. 有下载图片，并且（没有images 或者 有animateImage选项）， 并且相应方法
                    1. 在全局队列中转换图片
                    2. 转换完成，imageCache中存取
                    3. 执行完成回调
                5. 剩下的
                    1. 判断下载图片并完成，存图片
                    2. 执行完成回调
            5. 完成了，从running中移除
        17. 设置operation的cancellBlock
    18. 如果有cacheImage,调用完成的方法
        19. 调用完成方法，从移除runningOperations
    20. 没有cachedIamge,委托又不响应方法
        21. 调用完成方法，从移除runningOperations

(5)
```
- (nullable SDWebImageDownloadToken *)downloadImageWithURL:(nullable NSURL *)url
                                                   options:(SDWebImageDownloaderOptions)options
                                                  progress:(nullable SDWebImageDownloaderProgressBlock)progressBlock
                                                 completed:(nullable SDWebImageDownloaderCompletedBlock)completedBlock
```
1. weakSelf
2. 创建SDWebImageDownloadToken，并返回
    1. strongSelf
    2. 设置超时时间
    3. 创建request
    4. 设置cookies
    5. 设置管道连接
    6. 设置headerFields
    7. 创建downloadOperation
    8. 设置operation解压图片
    9. 设置operation证书或者设置用户名和密码
    10. 设置队列优先级
    11. 将operation添加进队列
    12. 如果有顺序选项，通过设置依赖，实现串行
    13. 返回operation


(6)
```
- (nullable SDWebImageDownloadToken *)addProgressCallback:(SDWebImageDownloaderProgressBlock)progressBlock
                                           completedBlock:(SDWebImageDownloaderCompletedBlock)completedBlock
                                                   forURL:(nullable NSURL *)url
                                           createCallback:(SDWebImageDownloaderOperation *(^)())createCallback
```
1. 判断url,若为空，调用block返回nil
2. 设置一个block类型的SDWebImageDownloadToken
3. dispatch_barrier_sync,将队列中的任务执行完。才执行这个任务，这个任务执行完。再加入下一个任务执行
4. 从URLOperations中根据url取出operation
5. 如果不存在创建一个
6. 加入到URLOperations中去
7. 设置operation的完成回调
    1. 强引用
    2. 判空
    3. 判断URLOperations，然后移除
8. 将progressBlock和completeBlock添加到operation中去，并返回一个cancelToken
9. 设置token
10. 设置url
11. 设置cancelToken
12. 返回token

(7) 殊途同归，强
这里的SDWebImageDownloader <---> AFURLSessionManager
这里的SDWebImageDownloaderOperation <---> AFURLSessionManagerTaskDelegate

都是通过Downloader,设置一个session,delegate为self,然后在协议方法中根据dataTask取出对应的DownloaderOperation，进一步分发协议方法
