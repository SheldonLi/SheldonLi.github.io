# AFNetworking解析By draveness
---
layout: post
title: AFNetworking解析By draveness
date: 2017-08-16 19:54:41.000000000 +08:00
---

摘自[blog](http://draveness.me/)系列：[AFNetworking 概述（一）](http://draveness.me/afnetworking1.html) 等


1. 概述
![AFNetworking结构](http://7xrlu3.com1.z0.glb.clouddn.com/2016-03-21-afnetworking-arch.png)

2. AFURLSessionManager 是 AFNetworking 的核心。

    1. 负责创建和管理 NSURLSession
        
        初始化方法：initWithSessionConfiguration:中
        - 初始化会话配置（NSURLSessionConfiguration），默认为defaultSessionConfiguration
        - 初始化会话（session），并设置会话的代理以及代理队列
        - 初始化管理响应序列化（AFJSONResponseSerializer），安全认证（AFSecurityPolicy）以及监控网络状态（AFNetworkReachabilityManager）的实例
        - 初始化保存 data task 的字典（mutableTaskDelegatesKeyedByTaskIdentifier）与锁对象 **用于标识请求task**
        - 置空task关联的代理(getTasksWithCompletionHandler:) 作用：为了从后台回来，重新初始化session，防止一些之前的后台请求任务，导致程序的crash。
    
    2. 管理 NSURLSessionTask
        获得了 AFURLSessionManager 的实例之后，我们可以通过以下方法创建 NSURLSessionDataTask 的实例
        - dataTaskWithRequest:uploadProgress:downloadProgress:completionHandler:        
        - uploadTaskWithRequest:fromData:progress:completionHandler:
        等

    3. 实现 NSURLSessionDelegate 等协议中的代理方法
        NSURLSessionDelegate, NSURLSessionTaskDelegate, NSURLSessionDataDelegate, NSURLSessionDownloadDelegate
        实现这些代理方法，提供更简洁的 block 的接口：
    
    4. 使用 AFURLSessionManagerTaskDelegate 管理进度
        KVO跟踪
        在每一个 NSURLSessionTask 结束时，都会在代理方法 URLSession:task:didCompleteWithError: 中：
        1. 调用传入的 completionHander block
        2. 发出 AFNetworkingTaskDidCompleteNotification 通知
    
    5. 使用 _AFURLSessionTaskSwizzling 调剂方法
        _AFURLSessionTaskSwizzling 的唯一功能就是修改 NSURLSessionTask 的 resume 和 suspend 方法，替换原有的实现。这样做的目的是为了在方法 resume 或者 suspend 被调用时发出通知。
    
    6. 引入 AFSecurityPolicy 保证请求的安全
    7. 引入 AFNetworkReachabilityManager 监控网络状态


3. 处理请求和响应 AFURLSerialization
    
    1. AFURLResponseSerialization 协议
    其实在整个 AFNetworking 项目中并不存在 AFURLResponseSerialization 这个类，这只是一个协议，遵循这个协议的类会将数据解码成更有意义的表现形式。
    遵循这个协议的类同时也要遵循 NSObject、NSSecureCoding 和 NSCopying 这三个协议，实现安全编码、拷贝以及 Objective-C 对象的基本行为

    2. AFHTTPResponseSerializer 类
        AFHTTPResponseSerializer 是在AFURLResponseSerialization 模块中最基本的类（因为 AFURLResponseSerialization 只是一个协议） AFHTTPResponseSerializer对 AFURLResponseSerialization 协议的实现
    验证响应的有效性：
    AFHTTPResponseSerializer 中方法的实现最长，并且最重要的就是 - [AFHTTPResponseSerializer validateResponse:data:error:]
    这个方法根据在初始化方法中初始化的属性 acceptableContentTypes 和 acceptableStatusCodes 来判断当前响应是否有效。
    
    
    3. AFJSONResponseSerializer 类
     AFJSONResponseSerializer 这个继承自 AFHTTPResponseSerializer 类的实现。
     初始化方法只是在调用父类的初始化方法之后更新了 acceptableContentTypes 属性
     这个类中与父类差别最大的就是对 AFURLResponseSerialization 协议的实现。
     步骤：
        1. 验证请求的有效性
        2. 解决一个空格引起的 bug
        3. 序列化 JSON
        4. 移除 JSON 中的 null ： [mutableDictionary removeObjectForKey:key]


    4. AFURLRequestSerialization 协议
        这个文件中的大部分类都是为 AFHTTPRequestSerializer 服务的：
        1. 处理查询的 URL 参数
        2. 设置 HTTP 头部字段
        3. 设置请求的属性
        4. 分块上传

        AFHTTPRequestSerializer 会在 AHHTTPSessionManager 初始化时一并初始化，这时它会根据当前系统环境预设置一些 HTTP 头部字段 Accept-Language User-Agent。
        同时它还对一些属性进行 KVO，确保它们在改变后更新 NSMutableURLRequest 中对应的属性。

    
    4. AFNetworkReachabilityManager 监控网络状态

        AFNetworkReachabilityManager 是对 SystemConfiguration 模块的封装，苹果的文档中也有一个类似的项目 Reachability 这里对网络状态的监控跟苹果官方的实现几乎是完全相同
        使用步骤：
            1. 初始化 AFNetworkReachabilityManager
            2. 调用 startMonitoring 方法开始对网络状态进行监控
            3. 设置 networkReachabilityStatusBlock 在每次网络状态改变时, 调用这个 block
        
        在实际的使用中，我们也可以直接操作 AFURLSessionManager 的 reachabilityManager 来获取当前的网络可达性状态，而不是自己手动初始化一个实例，当然这么做也是没有任何问题的。
    
    5. 验证 HTTPS 请求的证书
        使用 AFSecurityPolicy 时，总共有三种验证服务器是否被信任的方式（AFSSLPinningMode）：
        1. AFSSLPinningModeNone 是默认的认证方式，只会在系统的信任的证书列表中对服务端返回的证书进行验证
        2. AFSSLPinningModePublicKey 需要客户端预先保存服务端的证书
        3. AFSSLPinningModeCertificate 也需要预先保存服务端发送的证书，但是这里只会验证证书中的公钥是否正确
    
        AFSecurityPolicy 同样也作为一个即插即用的模块，在 AFNetworking 中作为验证 HTTPS 证书是否有效的模块存在，在 iOS 对 HTTPS 日渐重视的今天，在我看来，使用 HTTPS 会成为今后 API 开发的标配。



