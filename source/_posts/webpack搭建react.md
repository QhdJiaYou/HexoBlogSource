---
title: 无脚手架搭建react
date: 2020-11-06 17:50:43
---

新建空文件夹

初始化项目：`npm init -y`

安装react核心库文件：

`npm install react react-dom`

安装webpack以及一个简单的web服务器提供代码修改后自动编译的功能：

`npm install webpack webpack-dev-server`

处理构建到dist文件夹需要：

`npm install html-webpack-plugin clean-webpack-plugin`

安装typescript

`npm install --save-dev typescript ts-loader`

安装babel语法转换器：@babel/preset-react提供转换JSX语法的功能

`npm install --save-dev @babel/preset-react`

`npm install --save-dev @babel/preset-typescript`



## 模块加载方案

<https://blog.csdn.net/weixin_41631970/article/details/89467548>

<https://www.jianshu.com/p/c9b29520fc6e>

<https://blog.csdn.net/weixin_42594068/article/details/81007384>

|          | CommonJS                                               | AMD                                       | CMD                                                     | ES6 module                                 |
| -------- | ------------------------------------------------------ | ----------------------------------------- | ------------------------------------------------------- | :----------------------------------------- |
| 全称     |                                                        | Async module definition                   | Common module definition                                |                                            |
| 使用场景 | 服务端                                                 | 前端                                      | 前端                                                    | 前端                                       |
| 加载方式 | 运行时同步加载                                         | 运行时异步加载                            | 运行时异步加载                                          | 编译时静态加载                             |
| 导出模块 | module.exports或exports                                | define(moduleName?,[dependency?],factory) | define(factory)                                         | export暴露对外的接口                       |
| 导入模块 | require(modulePath)                                    | require(dependency,factory)               | require(modulePath)且require时factory为函数时的一个参数 | import                                     |
| 优缺点   | 输出的是值的拷贝，输出之后，模块内部的变化不会影响输出 | 并行加载，加载速度快，不能保证加载顺序    | 懒加载，使用时才会执行，顺序可控                        | 输出的是值的引用，模块内部的变化会影响输出 |
| 实现     | nodejs                                                 | requirejs                                 | seajs                                                   | ES6                                        |

##### 以上几种模块打包对比总结：

1、CommonJS规范主要用于服务端编程，加载模块是同步的，这并不适合在浏览器环境，因为同步意味着阻塞加载，浏览器资源是异步加载的，因此有了AMD、CMD解决方案。
2、AMD规范在浏览器环境中异步加载模块，而且可以并行加载多个模块。不过，AMD规范开发成本高，代码的阅读和书写比较困难，模块定义方式的语义不顺畅。
3、CMD规范与AMD规范很相似，都用于浏览器编程，依赖就近，延迟执行，可以很容易在Node.js中运行。但是依赖SPM打包，模块的加载逻辑偏重。
4、ES6在语言标准的层面上，实现了模块功能，而且实现得相当简单，完全可以取代CommonJS和 CMD规范，成为浏览器和服务器通用的模块化解决方案。

## 浏览器缓存

### 什么是 HTTP 缓存？

浏览器缓存通常情况下指的就是 HTTP 缓存，目前浏览器都各自基于 RFC 标准实现了 HTTP 缓存能力，通过 HTTP Headers 执行对应的缓存策略。

如下图，对于一个缓存来说，可以分为 缓存存储 和 缓存应用 两个部分，缓存应用下根据规则又分为不同的缓存策略。

### （1） 缓存存储对象

通常 HTTP 缓存只适用于 idempotent request（可以理解为查询请求，也就是不更新服务端数据的请求），缓存的对象通常是 html文件、css、js、图片等通过 GET 请求返回的资源文件；

浏览器第一次发起对 /index.html 的请求：

1. 由于本地缓存为空，浏览器直接向服务器发起请求；
2. **服务器返回响应，并设置了一些响应头**来控制资源在浏览器的缓存行为；
3. 浏览器收到请求，将响应头与资源一起缓存，并将资源用于页面展示

通常在本地无禁止缓存的配置请款下，缓存是浏览器的默认行为。所以 HTTP 缓存体系更多的内容是对缓存校验，防止缓存并使用已经过期的资源。

### （2）强缓存

当浏览器发起请求时，首先从本地缓存中检索是否存在当前请求的副本，如果找到本地缓存并且符合使用条件，即可直接使用该副本，这就是强缓存。

我们依旧使用画出浏览器和服务端通信，模拟强缓存的过程：

1. 50 秒后浏览器再次发起对 /index.html 的请求，检索到缓存中的 GET /index.html 
2. 根据缓存中的 Cache-Control: max-age=200 ，表示资源在发起请求后的 200 秒内可以被直接使用，于是直接从缓存读取响应，不向浏览器发出请求。

#### Cache-control

Cache-Control 是 HTTP1.1 缓存策略中最重要的字段，可以由多个字段组合而成，下面是常用的值：​	

(1)**no-cache 与 no-store** 

no-cache 表示在使用本地资源缓存之前，必须先请求服务器判断资源是否已经过期，才能使用该缓存；

no-store 则是直接禁止浏览器以及所有中间缓存存储任何版本的返回响应；

(2)**max-age**

max-age 指令表示从请求发起，到其所设定的时间之内（单位：秒），可直接使用本地缓存。例如，max-age=60 表示可在接下来的 60 秒内缓存可以重用；

(3)**must-revalidate**

must-revalidate 和 no-cache 的含义相似，不同之处在于 must-revalidate 会在当前资源过期后验证缓存，而 no-cache 无视缓存设定时间立即发出请求验证；

所以  max-age=0, must-revalidate 与 no-cache 表示一个意思；

(4)**public 与  private**

public 客户端和代理服务器都可以缓存响应所有内容；

private 表示这些响应只为单个用户缓存，不允许任何中间代理对其进行缓存；

#### Expires

Expires是 HTTP/1.0 控制网页缓存的字段，是一个绝对时间点，例如：

> expires: Thu, 19 Nov 1981 08:52:00 GMT

表示相比与客户端本地时间，缓存在该绝对时间点之前有效，再次发起该请求时，如果客户端的时间小于Expires的值时，直接使用缓存。

**注意：**Expires 控制缓存的原理是使用客户端的时间与服务端返回的时间做对比，如果客户端与服务端的时间因为某些原因（例如时区不同；客户端和服务端有一方的时间不准确）发生误差，那么强制缓存则会直接失效，这样的话强制缓存的存在则毫无意义。

所以到了HTTP/1.1，Expire已经被Cache-Control替代，Cache-Control 中指定的缓存过期策略优先级高于 Expires，当它们同时存在的时候，后者会被覆盖掉。

### (3) 协商缓存

如果当前请求未命中强缓存，并不直接代表的资源已不可用，而需要询问服务器该资源是否可以继续使用：

- 如果服务端资源未发生过变化，没必要再下载一遍资源；
- 如果资源发生了变化，则返回最新的资源并替换旧资源；

这就是协商缓存的过程。

我们还是按照浏览器页面、浏览器缓存、服务器来模拟一下这个过程：

1. 距离第一次请求 220 秒后，浏览器再次发起对 /index.html 的请求，检索到本地有以 GET /index.html 为 key 值的资源，根据 max-age=200 本地缓存已过期；
2. 浏览器发起验证本地缓存的请求，将缓存标识携带在请求头 If-None-Match 上，缓存标识对应第一次请求响应头里的 Etag;
3. 服务器依据请求中的 If-None-Match 值校验，向客户端返回 304 Not Modified 状态码；
4. 浏览器被告知资源未发生变化，可以再延用 200 秒，于是直接使用缓存中的 /index.html；

所以，协商缓存的关键在于携带资源标识请求服务器，来判断资源是否发生变更。这一过程是由 ETag/If-None-Match 和 Last-Modified/If-Modified-Since 两对Header来管理的。

#### **Etag/If-None-Match**

- Etag 是由服务器端根据资源生成的一段 hash 字符串，第一次请求时响应头携带 ETag: abc；
- 之后的请求中，浏览器自动检测缓存的响应头是否包含 ETag，并追加到请求头 If-None-Match: abc；
- 服务端通过验证 ETag 是否一致来判断客户端缓存数据是否仍有效，进而决定是否要重发数据。

#### **Last-modified/If-Modified-Since**

- Last-modified 是 HTTP 1.0 中的缓存协议，表示服务器端资源的最后修改时间，与 ETag 一致，跟在资源响应的头部，在第一次请求后随资源一起缓存；
- 之后的请求浏览器追加请求头 If-Modified-Since ，即为之前缓存下的 Last-modified 时间。服务器端收到带 If-Modified-Since 的请求后会去和资源的最后修改时间对比。
- 若资源的最后修改时间大于 If-Modified-Since，说明资源被改动过，返回状态码 200 同时返回整个资源，反之返回 304，告知浏览器继续可继续使用缓存；



使用 Last-modified 也具有一些场景上的缺陷：

- 某些服务器不能精确的得到文件的最后修改时间；
- Last-modified 只能精确到秒；
- 如果在本地打开缓存文件，就会造成 Last-Modified 被修改，导致协商缓存判断出错；

而 Etag 要求资源字节级别的一致，属于强验证，相比于 Last-modified 优先级更高。

**注意**：实际使用 ETag/Last-modified 要注意保持一致性，做负载均衡和反向代理的话可能会出现不一致的情况。计算 ETag 也是需要占用资源的，如果修改不是过于频繁，看自己的需求用 Cache-Control 是否可以满足。



HTTP 缓存原理主要是由强缓存和协商缓存组成的体系，总计起来就是以下几点：

1. 当浏览器请求一个资源时，先通过 Expires 和 Cache-Control 判断强缓存是否生效，生效则直接使用缓存；
2. 若强缓存不生效，则发起请求与服务器进行协商缓存，协商缓存生效返回 304，不生效则返回 200 + 最新资源；
3. 协商缓存 ETag/If-None-Match 和 Last-Modified/If-Modified-Since 两对 Header 来实现；
4. HTTP 1.1 协议定义的头字段优先级高于 HTTP 1.0，通常两种字段都会设定来保证向后兼容；

HTTP 缓存原理并不复杂，也不存在标准的使用方式，不同场景需要按照实际情况配置不同的缓存策略，让缓存发挥到最大的作用。