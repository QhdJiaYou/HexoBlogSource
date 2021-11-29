---
title: BOM内容
date: 2020-05-06 17:50:43
---

## Window对象

以下都是window对象的一部分

### Document对象（DOM）

**属性：**

document.body : 获取当前文档的body标签元素

document.cookie : 设置或返回与当前文档有关的所有cookie

document.documentURI 设置或返回文档的URI

document.domain 返回文档的域名

**方法：**

document.createAttribute(‘attributeName’) : 创建一个属性节点

document.createElement(‘elementName’) : 创建元素节点

document.createTextNode(‘textContent’) 创建文本节点

document.querySelector(‘CSS selector’) 返回匹配CSS选择器的第一个节点

document.querySelectorAll(‘CSS selector’) 返回所有匹配的节点，返回NodeList对象

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

window.history是用来保存用户在一个会话期间的网站访问记录，并提供相应的方法进行追溯。其成员如下：

**属性**：length、state

**方法**：back() 、forward() 、go(num) 、pushState(stateData,title,url)、replaceState(stateData,title,url)

**事件**：onpopstate、onhashchange

**点击页面前进或后退按钮都会触发popstate事件，以下三个方法也都可以触发，事件对应的回调函数参数event可以访问state信息**

`history.back()` //回退到上一个访问记录

`history.forward()` //前进到下一个访问记录

`history.go(num)` //num为正数是前进，负数是后退，history.go(0)相当于刷新当前页面

**HTML5规范为history对象新添加了两个方法，用来在浏览历史中添加和修改记录，不能跨域，不会造成页面刷新，不会触发onpopstate事件**

`history.pushState() `//在history栈中添加一个新的网址，当前location.href变为新网址

`history.replaceState()` //修改当前的访问记录网址

**pushState()通过back()可以将网址修改为执行pushState操作之前的网址，而replaceState()则不能**。

**直接修改location.href是会引起页面刷新的，并且原来会话的history栈中当前网址往前的记录都会删除**。

以上两个方法的主要应用是配合AJAX，使得页面可以恢复在AJAX请求之前的状态，如果我们在页面中执行一个ajax操作，当操作成功（如更新页面的局部内容）后，我们通过代码调用pushState方法，设置一个新的url，这样看上去就像发起了一个全新的请求，实际上只是个ajax操作。这时回退按钮也能用了，可以通过编写代码，来响应这个回退按钮触发的popstate事件，在事件中让界面恢复到ajax请求之前的界面。

也就是为AJAX动态请求的页面提供了与之对应的url记录，从而避免了前进或后退按钮导致页面崩溃或不存在

## Navigator对象

navigator对象包含浏览器相关的信息

| appCodeName   | 返回浏览器的代码名                          |
| ------------- | ------------------------------------------- |
| appName       | 返回浏览器的名称                            |
| appVersion    | 返回浏览器的平台和版本信息                  |
| cookieEnabled | 返回指明浏览器中是否启用 cookie 的布尔值    |
| platform      | 返回运行浏览器的操作系统平台                |
| userAgent     | 返回由客户机发送服务器的user-agent 头部的值 |

## Screen对象

screen对象包含有关客户端显示屏的信息

| availHeight | 返回屏幕的高度（不包括Windows下面的任务栏） |
| ----------- | -------------- |
| availWidth  | 返回屏幕的宽度（不包括Windows下面的任务栏） |
| colorDepth  | 返回目标设备或缓冲器上的调色板的比特深度    |
| pixelDepth  | 返回屏幕的颜色分辨率（每象素的位数）        |
| height      | 返回屏幕总高度                      |
| width       | 返回屏幕总宽度              
## 存储对象

localStorage ： key-value键值对，没有过期时间

sessionStorage ： key-value键值对，关闭当前浏览器窗口失效





