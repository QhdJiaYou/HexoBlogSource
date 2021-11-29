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
| -------- | ------- | ------- | -------- | :--------|
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