---
title: BOM内容
date: 2020-05-06 17:50:43
---

## Window对象

以下都是window对象的一部分

### Document对象（DOM）

### Location对象

是window对象的一个部分，一般通过window.location属性访问

假设当前页面的URL是：http://example.com:1234/test/test.html#part2?name=John

通过下面属性可以设置或返回我们想要的URL部分字段，它们都是可读写的字符串类型

| 属性     | 描述                                     | 返回值                                                   |
| -------- | ---------------------------------------- | -------------------------------------------------------- |
| host     | 当前URL的主机名和端口号                  | 'example.com:1234'                                       |
| hostname | 当前URL的主机名                          | 'example.com'                                            |
| href     | 当前显示的文档的完整 URL                 | 'http://example.com:1234/test/test.html#part2?name=John' |
| pathname | 当前URL的路径部分                        | '/test/test.html'                                        |
| port     | 当前 URL 的端口部分                      | '1234'                                                   |
| protocol | 当前 URL 的协议                          | 'http:'                                                  |
| search   | 当前 URL 的查询部分（问号 ? 之后的部分） | '?name=John'                                             |
| hash     | URL 的锚部分（从 # 号开始的部分）        | '#part2?name=John'                                       |

### History对象

包含用户在浏览器窗口中访问过的URL

H5为history对象新添加了两个方法，用来在浏览历史中添加和修改记录

history.pushState()

history.replaceState()

区别在于，pushState()会将之前的地址记录在history对象中，通过back()可以返回前一页；而replaceState()则不能返回。



## Navigator对象

navigator对象包含浏览器相关的信息

| appCodeName   | 返回浏览器的代码名                          |
| ------------- | ------------------------------------------- |
| appName       | 返回浏览器的名称                            |
| appVersion    | 返回浏览器的平台和版本信息                  |
| cookieEnabled | 返回指明浏览器中是否启用 cookie 的布尔值    |
| platform      | 返回运行浏览器的操作系统平台                |
| userAgent     | 返回由客户机发送服务器的user-agent 头部的值 |
|               |                                             |

## Screen对象

screen对象包含有关客户端显示屏的信息

| availHeight | 返回屏幕的高度（不包括Windows下面的任务栏） |
| ----------- | ------------------------------------------- |
| availWidth  | 返回屏幕的宽度（不包括Windows下面的任务栏） |
| colorDepth  | 返回目标设备或缓冲器上的调色板的比特深度    |
| pixelDepth  | 返回屏幕的颜色分辨率（每象素的位数）        |
| height      | 返回屏幕总高度                              |
| width       | 返回屏幕总宽度                              |
|             |                                             |

## 存储对象

localStorage ： key-value键值对，没有过期时间

sessionStorage ： key-value键值对，关闭当前浏览器窗口失效





