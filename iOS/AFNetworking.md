#AFNetworking[传送](https://github.com/AFNetworking)

1. 本地化字符串的宏
NSLocalizedStringFromTable(key, table, comment)

2. AFNetworkReachabilityManager
位运算
inline函数使用
主线程发通知

3. 调用流程
    (1)
    ```
    - (NSURLSessionDataTask *)GET:(NSString *)URLString
                       parameters:(id)parameters
                          success:(void (^)(NSURLSessionDataTask *task, id responseObject))success
                          failure:(void (^)(NSURLSessionDataTask *task, NSError *error))failure
    ```
    调用get

    (2)
    ```
    - (NSURLSessionDataTask *)GET:(NSString *)URLString
                       parameters:(id)parameters
                         progress:(void (^)(NSProgress * _Nonnull))downloadProgress
                          success:(void (^)(NSURLSessionDataTask * _Nonnull, id _Nullable))success
                          failure:(void (^)(NSURLSessionDataTask * _Nullable, NSError * _Nonnull))failure
    ```
    继续调用get

    (3)
    ```
    - (NSURLSessionDataTask *)dataTaskWithHTTPMethod:(NSString *)method
                                           URLString:(NSString *)URLString
                                          parameters:(id)parameters
                                      uploadProgress:(nullable void (^)(NSProgress *uploadProgress)) uploadProgress
                                    downloadProgress:(nullable void (^)(NSProgress *downloadProgress)) downloadProgress
                                             success:(void (^)(NSURLSessionDataTask *, id))success
                                             failure:(void (^)(NSURLSessionDataTask *, NSError *))failure
    ```
    requestSerializer构造request

    (4)
    ```
    - (NSURLSessionDataTask *)dataTaskWithRequest:(NSURLRequest *)request
                                   uploadProgress:(nullable void (^)(NSProgress *uploadProgress)) uploadProgressBlock
                                 downloadProgress:(nullable void (^)(NSProgress *downloadProgress)) downloadProgressBlock
                                completionHandler:(nullable void (^)(NSURLResponse *response, id _Nullable responseObject,  NSError * _Nullable error))completionHandler
    ```
    self.session 设置了delegate为self，然后在代理方法中分发给AFURLSessionManagerTaskDelegate代理

    (5)
    ```
    - (void)addDelegateForDataTask:(NSURLSessionDataTask *)dataTask
                    uploadProgress:(nullable void (^)(NSProgress *uploadProgress)) uploadProgressBlock
                  downloadProgress:(nullable void (^)(NSProgress *downloadProgress)) downloadProgressBlock
                 completionHandler:(void (^)(NSURLResponse *response, id responseObject, NSError *error))completionHandler
    ```
    设置AFURLSessionManagerTaskDelegate的各种闭包属性，并将它们放入到字典中，并且存取的时候加锁

4. NSProgress待研究
5. gcd的信号量待研究
6. gcd的group待研究