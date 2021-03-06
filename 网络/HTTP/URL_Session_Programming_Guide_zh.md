# [译] URL Session Programming Guide

参考译文: [URL加载系统 ](http://southpeak.github.io/2014/07/11/url-load-system-1/) by 南峰子 2014

目录
<!-- TOC -->

- [1. 关于 URL 加载系统](#1-关于-url-加载系统)
    - [1.1. 初见](#11-初见)
        - [URL 加载](#url-加载)
        - [辅助类](#辅助类)
        - [重定向](#重定向)
        - [认证与证书](#认证与证书)
        - [Cache Management](#cache-management)
        - [Cookie 存储](#cookie-存储)
        - [Protocol Support](#protocol-support)
    - [1.2. 如何使用该文档](#12-如何使用该文档)
- [2. 使用 NSURLSession](#2-使用-nsurlsession)
    - [2.1. 理解 URL Session 的概念](#21-理解-url-session-的概念)
        - [会话类型](#会话类型)
        - [任务类型](#任务类型)
        - [后台传输注意事项](#后台传输注意事项)
    - [2.2. 创建并配置会话](#22-创建并配置会话)
- [3. 编码与解码 URL 数据](#3-编码与解码-url-数据)
- [4. 重定向与其他请求变更](#4-重定向与其他请求变更)
- [5. 认证与 TLS 链验证](#5-认证与-tls-链验证)
    - [5.1. 决定如何响应一个认证请求](#51-决定如何响应一个认证请求)
- [6. 理解缓存](#6-理解缓存)
- [7. Cookie 与自定义协议](#7-cookie-与自定义协议)
- [8. URL Session 生命周期](#8-url-session-生命周期)

<!-- /TOC -->

## 1. 关于 URL 加载系统

这篇指南描述一些 Foundation 框架中的类。这些类与 URL 相关并可以通过标准的 Internet 协议与服务端进行通信。这些类通常被统称为 URL 加载系统。

URL 加载系统是类与协议的集合。这些类帮助我们可以通过 URL 访问内容。`NSURL` 类处于核心地位，通过它我们可以操作 URL 和其指向的资源。

我们可以使用 `Foundation` 提供的类加载 URL 的内容、上传数据至服务端、管理 cookie 存储、控制响应缓存、处理证书存储和认证、实现自定义协议扩展。

URL 加载系统支持通过以下协议访问资源：
- File Transfer Protocol (ftp://)
- Hypertext Transfer Protocol (http://)
- Hypertext Transfer Protocol with encryption (https://)
- Local file URLs (file:///)
- Data URLs (data://)

还支持代理服务和 SOCKS 网关。

> Important: On Apple platforms, a networking security feature called App Transport Security (ATS) is available to apps and app extensions, and is enabled by default. It improves privacy and data integrity by ensuring your app’s network connections employ only industry-standard protocols and ciphers without known weaknesses.
For more information, see NSAppTransportSecurity.

> Note: In addition to the URL loading system, OS X and iOS provide APIs for opening URLs in other applications, such as Safari. These APIs are not described in this document.
For more information about Launch Services in OS X, read Launch Services Programming Guide.
For more information about the openURL: method in the NSWorkSpace class in OS X, read NSWorkspace Class Reference.
For more information about the openURL: method in the UIApplication class in iOS, read UIApplication Class Reference.

### 1.1. 初见
The URL loading system includes classes that load URLs along with a number of important helper classes that work with those URL loading classes to modify their behavior. The major helper classes fall into five categories: protocol support, authentication and credentials, cookie storage, configuration management, and cache management.

![image](https://developer.apple.com/library/content/documentation/Cocoa/Conceptual/URLLoadingSystem/Art/nsobject_hierarchy_2x.png)

#### URL 加载
URL 加载系统中最常用的类允许我们通过 URL 从源获取内容。我们可以使用 `NSURLSession` 获取内容。你应该使用哪个方法很大程度上取决于是要将数据保存到内存还是下载到磁盘。

有两种方式从 URL 获取数据：
- 对于简单的请求，使用 `NSURLSession` API直接从 `NSURL` 对象获取数据，数据可以是 `NSData` 对象也可以是一个在磁盘上的文件。
- 对于更复杂的请求（比如上传数据）需要提供一个 `NSURLRequest` 或 `NSMutableURLRequest` 对象给 `NSURLSession`。

无论你选择哪种方式，我们都有两种方式来获得响应数据：
- block
- delegate

除了数据本身，响应对象还封装了与请求相关的元数据，比如 MIME 类型和内容长度。

> 注：通过 `NSURLSession` 下载不会有缓存。如果需要缓存，我们必须自己将数据写入磁盘。

#### 辅助类


#### 重定向
一些协议，比如 HTTP，允许服务端告知我们访问的内容已经被移到了另一个 URL。URL 加载类能够通过 delegate 通知我们。如果我们提供了合适的代理方法，我们可以决定是否要重定向，返回重定向后的响应结果还是返回错误。

#### 认证与证书
一些服务器对某些内容的访问有限制，需要用户提供证书来通过认证并获得访问权限。

#### Cache Management

#### Cookie 存储
由于 HTTP 是一种无状态协议，客户端经常使用 cookies 来提供 URL 请求数据的持久存储。URL 加载系统提供了用来创建和管理 cookies、将 cookies 作为 HTTP 请求的一部分发出、从 Web 服务端响应中取出 cookies。OS X 和 iOS 提供 `NSHTTPCookieStorage` 类来管理 `NSHTTPCookie` 对象。在 OS X 中，cookie 存储被所用应用共享；而 iOS 中，cookie 存储是每个应用独立一份。

#### Protocol Support

### 1.2. 如何使用该文档
首先阅读 [使用 NSURLSession](#2-使用-nsurlsession) 了解 URL 加载系统。然后阅读 [URL Session 生命周期](#8-url-session-生命周期) 来深入学习 `NSURLSession` 是如何与其 delegates 相互作用的。以下章节提供了关于 URL 加载系统其他方面的信息：
- [编码与解码 URL 数据](#3-编码与解码-url-数据) 介绍如何对字符串进行编码使其能够安全的在 URL 中使用
- [重定向与其他请求变更]() 介绍当你的请求发生变化时你能做什么
- [认证与 TLS 链验证]() 介绍如何验证你与服务器之间的安全连接
- [理解缓存]() 介绍在一次请求中如何使用缓存
- [Cookie 与自定义协议]() 介绍可用于管理 Cookie 存储和自定义应用层协议的类

## 2. 使用 NSURLSession
`NSURLSession` 及其相关类提供通过 HTTP 协议下载数据的 API。该 API 提供了丰富的代理方法来支持认证和后台下载（程序未运行或挂起时）。

使用 `NSURLSession` API，应用需要创建一系列的会话，每个会话负责协调一组相关数据的传输任务。比如你正在写一个 web 浏览器，我们需要为每个页签或者窗口创建一个会话。在每个会话中，添加一系列的任务，每个任务都表示一个对指定 URL 的请求。

与大多数网络 API 一样，`NSURLSession` API 是异步的。如果我们使用系统提供的代理，我们必须提供一个请求完成处理 block，以便在请求成功或失败时返回数据给我们的应用。如果我们提供自定义的代理对象，则任务对象调用这些代理方法，并回传从服务端获取的数据（如果是文件下载，则当传输完成时调用）。

> 注：完成后回调（Completion callbacks）可以作为自定义 delegate 的一种替代方案。如果我们使用回调这种方式来创建任务，则 delegate 将不会被调用。

`NSURLSession` API 提供了 status 和 progress 属性，并作为额外的信息传递给代理。同时它支持取消、重启（恢复）、挂起操作，并支持断点续传功能。

### 2.1. 理解 URL Session 的概念
会话（Session）中任务（Task）的行为取决于三点：会话的类型、任务的类型以及创建任务时应用是否处于前台。

#### 会话类型
`NSURLSession` 支持三种类型的会话（取决于创建会话时的配置对象）
- 默认会话（Default sessions）：行为与其它下载 URL 的 Foundation 方法类似。使用基于磁盘的缓存策略，并在用户 keychain 中存储证书。
- 短暂会话（Ephemeral sessions）：不存储任何数据在磁盘中；所有的缓存、证书存储等都保存在内存中并与会话绑定。这样，当应用结束会话时，它们被自动释放。
- 后台会话（Background sessions）：类似于默认会话，除了有一个独立的进程来处理所有的数据传输。

#### 任务类型
一个会话中，`NSURLSession` 支持三种类型的任务
- 数据任务（data tasks）：使用 `NSData` 对象来发送和接收数据，数据可以分片返回，也可以完成后一次性返回。
- 下载任务（download tasks）：以文件的形式接收数据，支持后台下载。
- 上传任务（upload tasks）：以文件的形式发送数据，支持后台上传。

#### 后台传输注意事项
`NSURLSession` 支持在程序挂起时在后台传输数据。后台传输只由使用后台会话配置对象创建的会话提供(调用 `backgroundSessionConfiguration:`)。

使用后台会话时，由于实际传输是在一个独立的进程中传输，且重启应用进程相当损耗资源，只有少量特性可以使用，所以有以下限制：
- 会话必须提供用于事件分发的代理
- 只支持 HTTP 和 HTTPS 协议（不支持自定义协议）
- 总是伴随着重定义操作
- 仅支持从文件上传的任务（程序退出时从数据对象或者流上传的任务将失败）
- 如果当应用在后台时初始化的后台传输，则配置对象的 discretionary 属性为 true

> 注：iOS 8 和 OS X 10.10 以前，后台会话不支持数据任务。

### 2.2. 创建并配置会话
`NSURLSession` 提供了丰富的配置选项：
- 支持缓存、cookie、认证及协议的私有存储
- 认证
- 上传下载文件
- 每个主机的连接的最大数
- 超时时间
- 支持的 TLS 最大和最小版本
- 自定义代理字典
- 控制 cookie 策略
- 控制 HTTP 管道行为

由于大部分设置都包含在一个独立的配置对象中，所以我们可以重用这些配置。当我们初始化一个会话对象时，我们指定了如下内容：
- 一个配置对象。用于管理会话和任务的行为
- 一个代理对象（可选）。用于处理接收的数据及其它事件，如服务端认证、决定一个资源加载请求是否应该转换成下载等。

如果我们不提供一个代理对象，`NSURLSession` 将使用系统提供的代理。In this way, you can readily use NSURLSession in place of existing code that uses the sendAsynchronousRequest:queue:completionHandler: convenience method on NSURLSession.（译者注：未找到该方法）

> 注：如果我们的应用需要执行后台传输，必须提供一个自定义的代理对象。

当实例化一个会话对象后，我们无法改变配置或者代理，除非创建新的会话。

示例程序 1-2 
``` Objective-C
// 创建会话配置
NSURLSessionConfiguration *defaultConfiguration = [NSURLSessionConfiguration defaultSessionConfiguration];
NSURLSessionConfiguration *ephemeralConfiguration = [NSURLSessionConfiguration ephemeralSessionConfiguration];
NSURLSessionConfiguration *backgroundConfiguration = [NSURLSessionConfiguration backgroundSessionConfigurationWithIdentifier: @"com.myapp.networking.background"];
 
// 为默认会话配置缓存行为
NSString *cachesDirectory = NSSearchPathForDirectoriesInDomains(NSCachesDirectory, NSUserDomainMask, YES).firstObject;
NSString *cachePath = [cachesDirectory stringByAppendingPathComponent:@"MyCache"];
 
/* Note:
 iOS requires the cache path to be
 a path relative to the ~/Library/Caches directory,
 but OS X expects an absolute path.
 */
#if TARGET_OS_OSX
cachePath = [cachePath stringByStandardizingPath];
#endif
 
NSURLCache *cache = [[NSURLCache alloc] initWithMemoryCapacity:16384 diskCapacity:268435456 diskPath:cachePath];
defaultConfiguration.URLCache = cache;
defaultConfiguration.requestCachePolicy = NSURLRequestUseProtocolCachePolicy;
 
// 创建会话
id <NSURLSessionDelegate> delegate = [[MySessionDelegate alloc] init];
NSOperationQueue *operationQueue = [NSOperationQueue mainQueue];
 
NSURLSession *defaultSession = [NSURLSession sessionWithConfiguration:defaultConfiguration delegate:delegate operationQueue:operationQueue];
NSURLSession *ephemeralSession = [NSURLSession sessionWithConfiguration:ephemeralConfiguration delegate:delegate delegateQueue:operationQueue];
NSURLSession *backgroundSession = [NSURLSession sessionWithConfiguration:backgroundConfiguration delegate:delegate delegateQueue:operationQueue];
```

## 3. 编码与解码 URL 数据

## 4. 重定向与其他请求变更

## 5. 认证与 TLS 链验证
一个 `NSURLRequest` 对象经常会遇到认证请求，或者需要从其所连接的服务端请求证书。当需要认证请求时，`NSURLSession` 会通知它们的代理对象，以便能正确地做处理。

### 5.1. 决定如何响应一个认证请求

## 6. 理解缓存

## 7. Cookie 与自定义协议

## 8. URL Session 生命周期