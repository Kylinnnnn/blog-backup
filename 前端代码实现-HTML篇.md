---
title: 前端代码实现_HTML篇
date: 2020-03-31 09:50:52
tags:
- HTML
- 前端
---
昨天字节一面。虽说是第一次面试，但也感受到自己在技术上的缺陷，尤其是在代码实现方面。许多功能的原理都懂，但是实现起来却不大会，还有些数组、字符串之类的常用方法，用起来也不熟悉。然而意外的是一面过了。然后想抓抓实践就特意把二面推到下周，能完成这个系列的话面试应该也会比较轻松。我自己还没看到`vue`，就暂时想分成`HTML`、`CSS`、`Javascript基础和进阶`三个模块。内容主要就是纯手写去实现一些常用的功能,其次就是一些我之前还不甚了解的知识点。
<!--- more --->
### (1).访问网站的过程
现在才意识到这个问题实际上想问的就是cs架构通信的内容。
1.利用DNS协议进行域名解析
2.建立tcp连接（延申出三次握手等知识点）
3.客户端相被访问网站发送请求（发送http请求报文）
4.服务端返回http内容
5.客户端收到html文件并对页面进行渲染
### (2).tcp三次握手与四次挥手
三次握手：
1.C2S，发送建连请求，S确认自己可以收到来自C的数据包。
2.S2C，发送建连确认，C确认自己能够收到来自S的数据包并且自己发送的的数据包成功到达了S。
3.C2S，发送C已准备就绪的消息，S确认自己发送的数据包成功到达了C，建连完成，双方均进入就绪状态`Established`。
四次挥手：
1.S2C，发送断连请求，S表示要断开连接，并且自己不会再发送数据（FIN标志位）。
2.C2S，发送断连确认，C进行要断开连接的确认。
3.C2S，发送断连请求，C表示自己也已断开连接，并且不会再发送数据。
4.S2C，发送断连确认，S进行断开连接的确认，tcp连接由此断开。
虽然握手时往往时客户端先向服务器发送请求，但断连时双方都是有可能的，对于长连接来说有时就是客户端先发送断连的请求，这里只是举个例子。
### (3).http请求报文结构
http请求报文结构从整体顺序来看是：
①请求方法（post\get）
②请求url
③HTTP协议及版本
④报文头
⑤报文体
①②③在第一行称为请求行，④为请求头，⑤为请求体。
关于报文头部分，具体含义没有细细研究。列举一些感觉比较重要的。
* `Content-Length`:表示消息正文的长度。
* `Host`:客户端想要访问的主机`ip`和端口号。
* `Referer`:客户端通过这个头告诉服务器，它是从哪个资源来访问服务器的（防盗链）。包含一个URL，用户从该URL代表的页面出发访问当前请求的页面
* `Cookie`:客户端可以通过这个头向服务端发送数据。一般用来传递`sessionID`。
* `Connection`:处理完此次请求后是断开连接还是继续连接。
* `User-agent`:包含发出请求的用户信息，我只能认出来里面有浏览器类型。记得好像是可以获取到这个值对用户的浏览手段进行辨别然后返回最合适的页面，在就是在安全方面也有作用。
### (4).http响应报文结构
http响应报文结构也分为三部分。
**响应行**：
①报文协议及版本
②状态码以及状态描述
**响应头**：
也举几个看到的感觉重要的
* `Allow`:服务器支持的请求方法。
* `Content-Encoding`:文档的编码方法。
* `Content-Type`:表示后面文档的类型及编码。
* `Content-Length`:表示内容长度。
* `Date`:当前时间。
* `Expires`:表示返回的资源缓存多长时间，`-1`或`0`表示不能缓存。
* `Set-Cookie`:设置和页面关联的`Cookie`。
**响应体**：
返回的数据、代码等。
### (5).http请求返回的状态码
1xx:表示成功接受请求并正在等待下一次请求。
2xx:表示成功接受请求并完成了整个处理过程。
3xx:表示需要重定向到另一个地址去获取资源。
4xx:表示客户端的请求出现了问题。
5xx:表示服务器端出现错误。
### (6).href属性与src属性的区别
`src`是从外部引入一个资源来替代当前标签位置的内容。`href`则是将当前标签与一个外部资源建立联系。引入和引用便是两者的区别。
### (7).浏览器的渲染原理
还从没了解过，先大致看看留个印象。
1.首先解析文档，构造DOM树。
2.然后解析CSS，构造CSSOM规则树。
3.根据DOM树和CSSOM规则树构建渲染树。渲染树的结点被称为渲染对象。
4.生成渲染树后根据渲染树进行布局（称为“回流”）。浏览器在这一阶段会弄清楚各个节点在页面中的确切位置和大小，这一行为也被称为“重排”。
5.布局结束之后是绘制阶段，遍历渲染树并调用渲染对象的`paint`方法将内容显示出来。

再给自己科普一下：render 渲染，大概是及物动词。
### (8).渲染过程中遇到JS文件
会暂停文档解析，将控制权交给`Javascript`引擎，待`Javascript`加载、解析、执行完之后在继续解析文档。
可以为`script`标签添加`defer`属性，有了该属性，会在解析文档的过程中并行加载和解析`Javascript`文件，但之后会等待文档解析完成之后在`DOMContentLoaded`事件触发之前按顺序执行。
```
<script defer type="text/javascript" src="xx.js"></script>
```
也可以为`script`标签添加`async`属性，有了该属性，`Javascript`文件的加载过程便不会阻塞，但执行过程仍会阻塞文档解析，并且多个脚本执行顺序无法保证。
```
<script async type="text/javascript" src="xx.js"></script>
```
### (9).渲染过程中遇到CSS文件
原本CSS是不会改变DOM结构的，因此并不会阻塞文件解析。但是由于`Javascript`文件有可能访问CSS资源，所以如果不解析CSS文件的话就会出错。
因此，当浏览器尚未完成CSSOM的加载和构建就遇到JS文件时，会延迟执行JS文件，转而先加载和构建CSSOM，然后再执行`Javascript`文件，最后再继续文档解析。
### (10).影响首次渲染的因素及优化方法
因素：
* 关键资源的数量。
* 关键路径的长度。
* 关键字节的数量。
优化方法：
1.对关键路径进行分析和特性描述：资源数、字节数、长度。（8懂为什么描述了能优化，省去了cpu计算这些东西的时间？感觉这个能做到的优化程度很有限）
2.最大限度减少关键资源的数量。删除、延迟下载或标记为异步等。
3.优化关键字节数以缩短下载时间。
4.优化其余关键资源的加载顺序，尽早下载关键资源。从而缩短关键路径。
### (11).重绘和回流
重绘：指当渲染树的一些元素需要更新属性，而这些属性不影响布局时。页面会发生重绘
回流：当渲染树因为一部分元素（或全部）的尺寸、布局、隐藏等需要重新构建时。页面会发生回流。

回流必定会发生重绘，重绘不一定会引发回流。回流所需的成本比重绘要高的多，改变父节点里的字节点很可能导致父节点里的一系列回流。

常见的引起重绘的属性和方法：
* color
* border-style
* visibility
* background
* text-decoration
* background-image
* background-position
* backgtound-repeat
* outline-color
* outline
* outline-style
* border-radius
* outline-width
* box-shadow
* background-size

常见的引起回流的属性和方法：
* width
* height
* margin
* padding
* display
* border
* position
* overflow
* clientWidth
* clientHeight
* clientTop
* clientLeft
* offsetWidth
* offsetHeight
* offsetLeft
* scrollWidth
* scrollHeight
* scrollTop
* scrollIntoView()
* scrollTo()
* getComputedStyle()
* getBoundingClientRect()
* scrollIntoViewIfNeeded()

操作DOM会影响页面性能就是因为会可能回引发页面的回流和重绘。
### (12)减少回流的方法
* 用`transform`替代`top`
* 不要把节点的属性值放在一个循环里作为循环里的变量
* 不要使用table布局（还没去考虑过布局）
* 把DOM离线后修改（见都还没见过）。
* 通过预先定义好`css`的`class`，然后修改DOM的`className`
### (13)常见的浏览器端的存储技术
有三种：`cookie`,`sessionStorage`,`localStorage`
`cookie`的使用：
```Javascript
    //创建cookie
    document.cookie = "username=kylin"; //在cookie中存入了一个叫kylin的username
    //创建有时效的cookie
    document.cookie = "age=21; expires=Thu, 18 Dec 2043 12:00:00 GMT";//创建了一个会在2043年12月18号12点过期的coockie
    //更改cookie
    document.cookie = "username=Fang";//将cookie中username键对应的值设置为Fang
    //删除cookie
    var day = new Date();//获取到当前时间
    document.cookie = "username=;expires="+day.toGMTString();//将过期时间设为现在即可
    
```
`cookie`的数据大小不能超过4k

`sessionStorage`的使用：
```Javascript
//创建一个sessionStorage储存
sessionStorage.setItem("username","kylin");
sessionStorage.username = "kylin";
sessionStorage["username"] = "kylin";

//访问一个sessionStorage储存
sessionStorage.getItem("username");
//或者之间通过.访问
console.log(sessionStorage.username);

//更改一个sessionStorage储存的值
//可以通过.访问去更改，也可以通过setItem()重新赋值
sessionStorage.username = "fang";
sessionStorage.setItem("username","kylin");

//删除一个sessionStorage储存
sessionStorage.removeItem("username");

//清除所有的sessionStorage数据
sessionStorage.clear();
```

`localStorage`的使用：
就用法上来说，跟`sessionStorage`完全一致。两者的差别我很清楚，就不写了。`:)`
### (14)iframe的缺点
* 会阻塞主页面的`onload`事件。`window`的`onload`事件需要在所有`iframe`加载完毕后才会触发。在`Safari`和`Chrome`，通过js动态设置iframe的src可以避免这种阻塞。
* `iframe`和主页面共享连接池，而浏览器对于相同域的连接有限制，所有会影响页面的并行加载。
* 浏览器的后退按钮失效
* 小型的移动设备无法完全显示框架
### (15)Label标签的使用
`label`标签定义表单控制间的关系，**当用户选择该标签时，浏览器会自动将焦点转到和标签相关的表单控件上**。
```HTML
<label for="Name">Number:</label>
<input type="text" name="Name" id="Name"/>
```
### (16)HTML5的form的自动完成功能
`autocomplete`属性默认启用。启动后允许浏览器预测字段的输入。
适用于`form`标签和以下`input`标签类型：`text`，`search`，`url`，`telephone`，`email`，`password`，`datepickers`，`range`，`color`。
### (17)如何实现浏览器内多个标签页之间的通信
前两个还算是看懂了，后两个见都没见过...
* 使用`WebSocket`，通信的标签页连接同一个服务器，发送消息到服务器后，服务器推送消息给所有连接的客户端。
* 可以调用`localStorage`、`cookies`等本地存储方式。`localStorage`在另一个浏览上下文里被添加、修改或删除时，都会触发一个`storage`事件，我们可以通过监听`storage`事件，控制它的值来进行页面信息通信。
* 使用`ShareWorker`，两个页面共享一个线程，通过线程发送和接受数据实现标签页之间的通信。
* 如果能够获得对标签页的引用，通过`postMessage`方法页可以实现多个标签页的通信。
### (18)如何在页面上实现一个圆形的可点击区域
纯`html`实现：
```HTML
<img src="xxx.jpg" usemap="#circle" />
<map name="circle">
    <area shape="circle" coords="100,200,30" href="xxxx.html">
</map>
```

纯`css`实现：
border-radius的值为长宽的一半以上时，形状即为圆形。

纯`js`实现
```Javascript
const r = 50;
const pointX = 100;
const pointY = 100;
document.onclick = function(e){
    var x = e.clientX;
    var y = e.clientY;
    var distance = Math.abs(Math.sqrt(Math.pow(x - pointX, 2)+ Math.pow(y - pointY, 2)));
    if(distance > 50){
        alert("Out of range!");
    }
}
```
### (19)实现不使用border画出1px高的线，在不同浏览器的标准模式与怪异模式下都能保持一致的效果。
```css
.div{
    overflow: hidden;
    background: black;
    height: 1px;
}
```
### (20)渐进增强和优雅降级的定义
渐进增强：针对低版本浏览器进行构建页面，保证最基本的功能，然后再针对高级浏览器进行效果、交互等改进和追加功能达到更好的用户体验。
优雅降级：一开始就根据高版本浏览器构建完整的功能，然后再针对低版本浏览器进行兼容。

### (21)Flash、Ajax各自优缺点，在使用中如何取舍
Flash:
* Flash适合处理多媒体、矢量图形、访问机器
* 对CSS、处理文本上不足，不容易被搜索
Ajax：
* Ajax对CSS、文本支持很好，支持搜索
* 多媒体、矢量图形、机器访问不足

共同点：
* 与服务的无刷新传递消息
* 可以检测用户离线和在线状态
* 操作DOM