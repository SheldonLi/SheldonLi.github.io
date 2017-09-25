---
layout: post
title: NSURLSessionConfiguration细节
date: 2017-09-10 21:06:31.000000000 +08:00
---

1. 作用：设置NSURLSession的相关配置，比如设置默认的HTTP头，配置缓存策略，限制使用蜂窝数据等
2. 常用设置：
    1. 初始化方法:
        - + (NSURLSessionConfiguration *)defaultSessionConfiguration; 凭证，缓存，cookie使用通用的缓存策略（默认配置使用的是持久化的硬盘缓存，存储证书到用户钥匙链，存储cookie到shareCookie）
        - + (NSURLSessionConfiguration *)ephemeralSessionConfiguration;凭证，缓存，cookie不持久化到磁盘缓存中
        - + (NSURLSessionConfiguration *)backgroundSessionConfigurationWithIdentifier:(NSString *)identifier;生成一个后台可以传输的任务
    2. 重要的属性:
        - allowsCellularAccess 是否允许使用手机网络（3G、4G）进行网络传输
        - HTTPAdditionalHeaders 请求头设置
        - discretionary 允许系统对后台任务进行优化（默认值是NO）
3. AFNetworking中的使用
    使用initWithBaseURL:sessionConfiguration:初始化以设置AFURLSessionManager内的session对象（注意：readonly属性）

4. 注意点
    - 当session创建时会拷贝一份configuration，所以之后无法通过修改configuration值更改session的设置
    - backgroundSessionConfigurationWithIdentifier初始化时，创建相同的identifier会有问题，需注意identifier写死并且有多任务传输的时候
    - 想在没有sim卡的手机测试“仅wifi传输”的时候，想到在手机设置里使用开发者的“NETWORK LINK CONDITIONER”配置为Cellular的Interface，结果这种配置没用，还是被当成使用wifi
    - 当discretionary=YES时，在iPhone7上传输任务不会马上执行，而是几分钟后的某个随机时间或者操作，此现象在IPhone7（10.3.3系统）上发现，别的机型暂无发现

5. 补充
    - 如果NSURLRequest中也做了一些指定。session也会遵循NSURLRequest的限定，但是如果configuration有更加严格的限定，仍以configuration为主。
    - 在后台会话中，NSURLSessionDataTasks 是完全不支持的，你应该只出于短期的，小请求为目的使用这些任务，而不是用来下载或上传。
    - 后台传输服务只支持HTTP和HTTPS，你不能使用自定义的协议。
    - timeoutIntervalForResource属性，支持资源超时特性。你可以使用这个特性指定你允许完成一个传输所需的最长时间。内容只在有限的时间可用，或者在用户只有有限Wifi带宽的时间内无法下载或上传资源的情况下，你也可以使用这个特性。

    
参考资料：
[iOS使用NSURLSession进行下载（包括后台下载，断点下载）](http://www.jianshu.com/p/1211cf99dfc3)

