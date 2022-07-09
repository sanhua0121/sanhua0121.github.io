---
created: 2022-07-09T21:14:16 (UTC +08:00)
tags: [Node.js]
source: https://juejin.cn/post/6857716625764777991
author: lanyan520
---

# Websocket原理及具体使用（ws+socket.io） - 掘金

> ## Excerpt
> 服务器和客户端保持长连接通信，实现方式比较多。有很多成熟的框架可以完成，底层无非都是对Socket流的封装和使用。 Socket大致是指在端到端的一个连接中，这两个端叫做Socket。 HTTP是基于传输层的TCP协议的，而Socket API也是，所以只是从使用上说，可以认为…

---
## 前言

服务器和客户端保持长连接通信，实现方式比较多。有很多成熟的框架可以完成，底层无非都是对Socket流的封装和使用。

## 一、SOCKET原理

Socket大致是指在端到端的一个连接中，这两个端叫做Socket。

HTTP是基于传输层的TCP协议的，而Socket API也是，所以只是从使用上说，可以认为Socket和HTTP类似（HTTP是成文的互联网协议，Socket是一直沿用的一种编程概念），是对于传输层协议的另一种直接使用。

### 1.1套接字（socket）概念

套接字（socket）是通信的基石，是支持TCP/IP协议的网络通信的基本操作单元。它是网络通信过程中端点的抽象表示， 包含进行网络通信必须的五种信息：

-   连接使用的协议，
-   本地主机的IP地址，
-   本地进程的协议端口，
-   远地主机的IP地址，
-   远地进程的协议端口

应用层通过传输层进行数据通信时，TCP会遇到同时为多个应用程序进程提供并发服务的问题。多个TCP连接或多个应用程序进程可能需要通过同一个 TCP协议端口传输数据。 为了区别不同的应用程序进程和连接，许多计算机操作系统为应用程序与TCP／IP协议交互提供了套接字(Socket)接口。应用层可以和传输层通过Socket接口，区分来自不同应用程序进程或网络连接的通信，实现数据传输的并发服务。

### 1.2 建立socket连接

建立Socket连接至少需要一对套接字，其中一个运行于客户端，称为ClientSocket ，另一个运行于服务器端，称为ServerSocket 。

套接字之间的连接过程分为三个步骤：服务器监听，客户端请求，连接确认。

**服务器监听**：服务器端套接字并不定位具体的客户端套接字，而是处于等待连接的状态，实时监控网络状态，等待客户端的连接请求。

**客户端请求**：指客户端的套接字提出连接请求，要连接的目标是服务器端的套接字。为此，客户端的套接字必须首先描述它要连接的服务器的套接字，指出服务器端套接字的地址和端口号，然后就向服务器端套接字提出连接请求。

**连接确认**：当服务器端套接字监听到或者说接收到客户端套接字的连接请求时，就响应客户端套接字的请求，建立一个新的线程，把服务器端套接字的描述发给客户 端，一旦客户端确认了此描述，双方就正式建立连接。而服务器端套接字继续处于监听状态，继续接收其他客户端套接字的连接请求。

> 注意，Socket并不是一种协议，更多的是为了方便开发者提供友好的编程方式。Socket通过门面模式实现了对TCP/IP的封装，它实际对上层提供了一系列的接口，以方便我们在自己的应用中操作，而不必理会复杂的TCP/IP处理。这一网络层次属于数据传输层。

![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/69658e2bc3334328bece44c72e10eac8~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

## 二、WebSocket

WebSocket protocol 是HTML5一种新的协议。目前除了IE浏览器，其他浏览器都基本支持。它实现了浏览器与服务器全双工通信(full-duplex)。一开始的握手需要借助HTTP请求完成。

他的目的是，即时通讯，替代轮询。网站上的即时通讯是很常见的，比如网页的QQ，聊天系统等。按照以往的技术能力通常是采用轮询、Comet技术解决。

### 2.1 轮询的缺点：

HTTP协议是非持久化的，单向的网络协议，在建立连接后只允许浏览器向服务器发出请求后，服务器才能返回相应的数据。当需要即时通讯时，通过轮询在特定的时间间隔（如1秒），由浏览器向服务器发送Request请求，然后将最新的数据返回给浏览器。

这样的方法最明显的缺点就是需要不断的发送请求，而且通常HTTP request的Header是非常长的，为了传输一个很小的数据 需要付出巨大的代价，是很不合算的，占用了很多的宽带。

HTTP1.1默认使用长连接，使用长连接的HTTP协议，会在响应头中加入下面这行信息: Connection:keep-alive

然而WebSocket的出现可以弥补这一缺点。在WebSocket中，只需要服务器和浏览器通过HTTP协议进行一个握手的动作，然后单独建立一条TCP的通信通道进行数据的传送。

### 2.2 WebSocket原理

WebSocket同HTTP一样也是应用层的协议，但是它是一种双向通信协议，是建立在TCP之上的。

WebSocket是一种在单个TCP连接上进行全双工通信的协议。WebSocket API也被W3C定为标准。

WebSocket使得客户端和服务器之间的数据交换变得更加简单，允许服务端主动向客户端推送数据。在WebSocket API中，浏览器和服务器只需要完成一次握手， 两者之间就直接可以创建持久性的连接，并进行双向数据传输。

**握手过程：**

1.  浏览器、服务器建立TCP连接，三次握手。这是通信的基础，传输控制层，若失败后续都不执行。
2.  TCP连接成功后，浏览器通过HTTP协议向服务器传送WebSocket支持的版本号等信息。（开始前的HTTP握手）
3.  服务器收到客户端的握手请求后，同样采用HTTP协议回馈数据。
4.  当收到了连接成功的消息后，通过TCP通道进行传输通信。

Websocket默认使用请求协议为:ws://,默认端口:80。对TLS加密请求协议为:wss://，端口:443。

### 2.3 webSocket与HTTP的关系

**相同点**：

1.  都是一样基于TCP的，都是可靠性传输协议。
2.  都是应用层协议。

**不同点**:

1.  WebSocket是双向通信协议，模拟Socket协议，可以双向发送或接受信息。
2.  HTTP是单向的。
3.  WebSocket是需要握手进行建立连接的。

**联系**： WebSocket在建立握手时，数据是通过HTTP传输的。但是建立之后，在真正传输时候是不需要HTTP协议的。

### 2.4 WebSocket与Socket的关系

Socket其实并不是一个协议，而是为了方便使用TCP或UDP而抽象出来的一层，是位于应用层和传输控制层之间的一组接口。tcp是可靠的连接，且连接后才可以发送数据；udp是不可靠的连接，不连接就可以发送数。

Socket是应用层与TCP/IP协议族通信的中间软件抽象层，它是一组接口。在设计模式中，Socket其实就是一个门面模式，它把复杂的TCP/IP协议族隐藏在Socket接口后面，对用户来说，一组简单的接口就是全部，让Socket去组织数据，以符合指定的协议。

当两台主机通信时，必须通过Socket连接，Socket则利用TCP/IP协议建立TCP连接。TCP连接则更依靠于底层的IP协议，IP协议的连接则依赖于链路层等更低层次。

WebSocket则是一个典型的应用层协议，Socket是传输控制层协议。

![](https://p1-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7ff088a763a0491396b4f21fdcdf5cd2~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

### 2.5 WebSocket 特点以及常见概念

-   支持浏览器/Nodejs环境
-   支持双向通信
-   API简单易用
-   支持二进制传输
-   减少传输数据量

#### 2.5.1 长轮询

http 长轮询是服务器收到请求后如果有数据, 立刻响应请求; 如果没有数据就会 hold 一段时间,这段时间内如果有数据立刻响应请求; 如果时间到了还没有数据, 则响应 http 请求;浏览器受到 http 响应后立在发送一个同样http 请求查询是否有数据; ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/3cee74bb9dd3464f8d519edd7fe93968~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

#### 2.5.2 短轮询:

客户端不管是否收到服务端的response数据，都会定时向服务端发送请求，查询是否有数据更新。 http 短轮询的局限是实时性低; ![](https://p9-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/424625e6eafc4be9a1f1b055bd49d08c~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

**长轮询与短轮询两者相同点**： 可以看出 http 长轮询和 http 短轮询的都会 hold 一段时间;

**长轮询与短轮询两者不同点** 间隔发生在服务端还是浏览器端: http 长轮询在服务端会 hold 一段时间, http 短轮询在浏览器端 “hold”一段时间; **应用**： http长轮询一般用在 web im, im 实时性要求高, http 长轮询的控制权一直在服务器端, 而数据是在服务器端的,因此实时性高;

http 短轮询一般用在实时性要求不高的地方, 比如新浪微薄的未读条数查询就是浏览器端每隔一段时间查询的. setInterval

#### 2.5.3 长连接

1.  长连接是指的TCP连接，而不是HTTP连接
2.  长连接意味着连接会被复用
3.  服务器和客户端都设置 Connection: keep-alive
4.  现在基本用的HTTP1.1协议，HTTP1.1默认长连接

所谓 http 长连接, 就是多个 http 请求共用一个 tcp 连接; 这样可以减少多次临近 http 请求导致 tcp建立关闭所产生的时间消耗. http 1.1 中在请求头和相应头中用 connection字段标识是否是 http长连接, connection: keep-alive, 表明是 http 长连接; connection:closed, 表明服务器关闭 tcp 连接.

与 connection 对应的一个字段是 keep-live, http 响应头中出现, 他的格式是 timeout=30,max=5, timeout 是两次 http 请求保持的时间(s), , max 是这个 tcp 连接最多为几个 http请求重用

指在一个TCP连接上可以发送多个数据包，在TCP连接保持期间，如果没有数据包发送，则双方就需要发送心跳包来维持此连接。 连接过程: 建立连接——数据传输——…——(发送心跳包，维持连接)——…——数据传输——关闭连接

**好处：**

比如请求一个普通的网页，这个网页里肯定包含了若干CSS、JS等一系列资源，如果是短连接（也就是每次都要重新建立TCP连接）的话，那每次打开一个网页，基本就要建立几个甚至几十个TCP连接，浪费很多网络资源。如果是长连接的话，那么这么多HTTP请求（包括请求网页的内容、CSS文件、JS文件、图片等）都是使用的一个TCP连接，显然可以节省很多资源。

另外一点，长连接并不是永久连接的。如果一段时间内（具体时间可以在header中进行设置，也就是所谓的超时时间），这个连接没有HTTP请求发出的话，那么这个长连接就会被断掉。

> 注意：因为HTTP1.1协议以后，连接默认都是长连接。没有短连接说法(HTTP1.0默认使用短连接)，网上大多数指的长短连接实质上说的就是TCP连接。

#### 2.5.4 短连接

指通信双方有数据交互时，建立一个TCP连接，数据发送完成之后，则断开此连接。 连接过程: 建立连接——数据传输——断开连接——…——建立连接——数据传输——断开连接

目前 http 协议普遍使用的是 1.1 版本, 之前有个 1.0 版本,两者之间的一个区别是 1.1 支持 http 长连接

http1.0 不支持 http 长连接, 每次一个 http请求响应后都关闭 tcp 连接, 下个 http 请求会重新建立 tcp 连接.

#### 2.5.5 通讯概念

-   单工

数据传输的方向唯一，只能由发送方向接收方的单一固定方向传输数据。

-   半双工

即通信双方既是接收方也是发送方，不过，在某一时刻只能允许向一个方向传输数据。

-   全双工:

即通信双方既是接收方也是发送方，两端设备可以同时发送和接收数据。

单工、半双工和全双工 这三者都是建立在 TCP协议(传输层上)的概念，不要与应用层进行混淆。

## 三、HTTP与TCP的区别和联系

### 3.1、TCP连接

建立起一个TCP连接需要经过“三次握手”：

第一次握手：客户端发送syn包(syn=j)到服务器，并进入SYN\_SEND状态，等待服务器确认；

第二次握手：服务器收到syn包，必须确认客户的SYN（ack=j+1），同时自己也发送一个SYN包（syn=k），即SYN+ACK包，此时服务器进入SYN\_RECV状态；

第三次握手：客户端收到服务器的SYN＋ACK包，向服务器发送确认包ACK(ack=k+1)，此包发送完毕，客户端和服务器进入ESTABLISHED状态，完成三次握手。

握手过程中传送的包里不包含数据，三次握手完毕后，客户端与服务器才正式开始传送数据。理想状态下，TCP连接一旦建立，在通信双方中的任何一方主动关闭连 接之前，TCP 连接都将被一直保持下去。断开连接时服务器和客户端均可以主动发起断开TCP连接的请求，断开过程需要经过“四次握手”（过程就不细写 了，就是服务器和客户端交互，最终确定断开）

### 3.2、HTTP连接

HTTP协议即超文本传送协议(Hypertext Transfer Protocol )，是Web联网的基础，也是手机联网常用的协议之一，HTTP协议是建立在TCP协议之上的一种应用。

HTTP连接最显著的特点是客户端发送的每次请求都需要服务器回送响应，在请求结束后，会主动释放连接。从建立连接到关闭连接的过程称为“一次连接”。

1）在HTTP 1.0中，客户端的每次请求都要求建立一次单独的连接，在处理完本次请求后，就自动释放连接。

2）在HTTP 1.1中则可以在一次连接中处理多个请求，并且多个请求可以重叠进行，不需要等待一个请求结束后再发送下一个请求。

由于HTTP在每次请求结束后都会主动释放连接，因此HTTP连接是一种“短连接”，要保持客户端程序的在线状态，需要不断地向服务器发起连接请求。通常的 做法是即时不需要获得任何数据，客户端也保持每隔一段固定的时间向服务器发送一次“保持连接”的请求，服务器在收到该请求后对客户端进行回复，表明知道客 户端“在线”。若服务器长时间无法收到客户端的请求，则认为客户端“下线”，若客户端长时间无法收到服务器的回复，则认为网络已经断开。

-   TCP是底层通讯协议，定义的是数据传输和连接方式的规范
    
-   HTTP是应用层协议，定义的是传输数据的内容的规范
    
-   HTTP协议中的数据是利用TCP协议传输的，所以支持HTTP也就一定支持TCP
    
-   HTTP支持的是www服务 ；
    
-   而TCP/IP是协议是Internet国际互联网络的基础。TCP/IP是网络中使用的基本的通信协议。
    

## 四、nodejs创建web服务器和Tcp服务器

### 4.1 使用Express创建Web服务器

```
var express = require('express');
var app = express();

app.use(express.static('./public'))
app.get('/',(req,res,next)=>{
  res.end('hello');
  next(); // 进行下一步，打印日志 
})

//Router方法 适合某一个模块下的多个子路由
var Router = express.Router();
Router.get('/add',(req,res)=>{
  res.end('add')
})
Router.get('/list',(req,res)=>{
  res.end('list')
})
app.use('/post', Router)

//route方法  适合restful API
app.route('/article')
  .get((req,res)=>{
    res.end('/article get')
  })
  .post((req,res)=>{
    res.end('/article post')
  })
app.get('/news/:newsId', (req, res)=>{
  req.end('newsId:' + req.newsId);
})    

app.listen(18001, function afterLister(){
  console.log('服务再次启动')
})

```

### 4.2 创建TCP服务器

使用Node.js创建TCP服务器，首先要使用require（‘net’）来加载net模块，之后使用net模块的createServer方法就可以创建一个TCP服务器.

-   使用net模块创建TCP服务器
-   使用telnet连接TCP服务器
-   使用net创建TCP客户端

下面代码构建一个TCP服务器：

```
//引入net模块
var net=require('net');
//创建TCP服务器
var server=net.createServer(function(socket){
    console.log('someone connets');
})
server.listen(18001,function(){
    console.log('server is listening');
});
```

当在浏览器中输入[http://localhost:18001/，连接成功后就会打印出someone](https://link.juejin.cn/?target=http%3A%2F%2Flocalhost%3A18001%2F%25EF%25BC%258C%25E8%25BF%259E%25E6%258E%25A5%25E6%2588%2590%25E5%258A%259F%25E5%2590%258E%25E5%25B0%25B1%25E4%25BC%259A%25E6%2589%2593%25E5%258D%25B0%25E5%2587%25BAsomeone "http://localhost:18001/%EF%BC%8C%E8%BF%9E%E6%8E%A5%E6%88%90%E5%8A%9F%E5%90%8E%E5%B0%B1%E4%BC%9A%E6%89%93%E5%8D%B0%E5%87%BAsomeone") connets字样，表明createServer方法的回调函数已经执行，说明已经成功连接这个TCP服务器。

**服务端：tcp.js:**

```
const HOST = '127.0.0.1';
//引入net模块
var net=require('net');

//创建TCP服务器
var server=net.createServer(function(socket){
    console.log('someone connets');
    //     //获取地址信息
    var address=server.address();
    //获取地址端口
    console.log('the port  is '+address.port);
    console.log('the address  is '+address.address);
    var message='client,the server address is'+JSON.stringify(address);
     //发送数据
     socket.write(message,function(){
        var writeSize=socket.bytesWritten; 
        console.log(message+'has send');
        console.log('the size of message is'+writeSize);
    });

    //监听dada事件
    socket.on('data',function(data){
        //打印data
        console.log(data.toString());
        socket.write('server write:test ')
    });
});
 //设置监听端口
server.listen(18001,HOST,function(){  
    console.log('server is listening');
});

```

启动服务 node tcp.js

**客户端 cmd**

1> 在cmd 控制台输入模拟客户端： telnet 127.0.0.1 18001

但是需要先通过控制面板把 telnet 勾选上,如下图所示： ![](https://p3-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/bee534b51da7474f8bf725eab9f139fb~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

2> 或者通过网络调试助手模拟客户端和服务端 调试 ![](https://p6-juejin.byteimg.com/tos-cn-i-k3u1fbpfcp/7e68c6f67a29461985b797b15bb374ff~tplv-k3u1fbpfcp-zoom-in-crop-mark:3024:0:0:0.awebp)

网络调试助手调试下载地址：

```
链接：https://pan.baidu.com/s/1bZQm1f9UtBKPSAIyqM4Nyw 
提取码：d9ck
```

参考：[www.onlinedown.net/soft/971066…](https://link.juejin.cn/?target=https%3A%2F%2Fwww.onlinedown.net%2Fsoft%2F971066.htm "https://www.onlinedown.net/soft/971066.htm")

**客户端 tcpClient.js：**

```
var net = require('net')

const PORT = 18001;
const HOST = '127.0.0.1';

var tcpClient = net.Socket();
tcpClient.connect(PORT, HOST, function(){
  console.log('客户端发送信息成功打印')
  tcpClient.write('客户端发送信息成功')
});

tcpClient.on('data',(data)=>{
  console.log( data.toString())
})

```

## 五、websocket 常用框架

-   socket.io
-   Ws

### 5.1 WebSocket事件：

WebSocket API是纯事件驱动，通过监听事件可以处理到来的数据和改变的链接状态。客户端不需要为了更新数据而轮训服务器。服务端发送数据后，消息和事件会异步到达。WebSocket编程遵循一个异步编程模型，只需要对WebSocket对象增加回调函数就可以监听事件。你也可以使用addEventListener()方法来监听。而一个WebSocket对象分四类不同事件。

#### 5.1.1 open:

一旦服务端响应WebSocket连接请求，就会触发open事件。响应的回调函数称为onopen。

```
ws.onopen = function(e) {
    console.log("Connection open...");
};
```

open事件触发的时候，意味着协议握手结束，WebSocket已经准备好收发数据。如果你的应用收到open事件，就可以确定服务端已经处理了建立连接的请求，且同意和你的应用通信。

#### 5.1.2 Message

当消息被接受会触发消息事件，响应的回调函数叫做onmessage。如下： // 接受文本消息的事件处理实例：

```
ws.onmessage = function(e) {
    if(typeof e.data === "string"){
    console.log("String message received", e, e.data);
    } else {
    console.log("Other message received", e, e.data);
}
};
```

除了文本消息，WebSocket消息机制还能处理二进制数据，有Blob和ArrayBuffer两种类型，在读取到数据之前需要决定好数据的类型。

```
// 设置二进制数据类型为blob（默认类型）
ws.binaryType = "blob";
// Event handler for receiving Blob messages
ws.onmessage = function(e) {
    if(e.data instanceof Blob){
    console.log("Blob message received", e.data);
    var blob = new Blob(e.data);
}
};
//ArrayBuffer
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
if(e.data instanceof ArrayBuffer){
    console.log("ArrayBuffer Message Received", + e.data);// e.data即ArrayBuffer类型
    var a = new Uint8Array(e.data);
}
};

```

#### 5.1.3 Error

如果发生意外的失败会触发error事件，相应的函数称为onerror,错误会导致连接关闭。如果你收到一个错误事件，那么你很快会收到一个关闭事件，在关闭事件中也许会告诉你错误的原因。而对错误事件的处理比较适合做重连的逻辑。

```
//异常处理
ws.onerror = function(e) {
   console.log("WebSocket Error: " , e);
};

```

#### 5.1.4 Close

不言而喻，当连接关闭的时候回触发这个事件，对应onclose方法，连接关闭之后，服务端和客户端就不能再收发消息。 当然你可以调用close方法断开与服务端的链接来触发onclose事件，

```
ws.onclose = function(e) {
    console.log("Connection closed", e);
};
```

### 5.2 WebSocket方法：

#### 5.2.1 send

一旦在服务端和客户端建立了全双工的双向连接，可以使用send方法去发送消息， //发送一个文本消息 ws.send("Hello WebSocket!");

当连接是open的时候send()方法传送数据，当连接关闭或获取不到的时候回抛出异常。

注意：必须是open之后才可以send数据

```
var ws = new WebSocket("ws://echo.websocket.org")
ws.onopen = function(e) {
    ws.send("Initial data");
}
```

如果想通过响应别的事件去发送消息，可以检查readyState属性的值为open的时候来实现。

```
function myEventHandler(data) {
if (ws.readyState === WebSocket.OPEN) {
    //open的时候即可发送
    ws.send(data);
} else {
    // Do something else in this case.
}
}
```

发送二进制数据：

```
// Send a Blob
var blob = new Blob("blob contents");
ws.send(blob);
// Send an ArrayBuffer
var a = new Uint8Array([8,6,7,5,3,0,9]);
ws.send(a.buffer);
```

     Blob对象和JavaScript File API一起使用的时候相当有用，可以发送或接受文件，大部分的多媒体文件，图像，视频和音频文件。这一章末尾会结合File API提供读取文件内容来发送WebSocket消息的实例代码。

#### 5.2.2 close()

使用close方法来关闭连接，如果连接以及关闭，这方法将什么也不做。调用close方法只后，将不能发送数据。 ws.close();

## 六、 websocket服务端基于ws的搭建

### 1.简单安装

安装ws模块，npm install ws

ws：是nodejs的一个WebSocket库，可以用来创建服务。 

[ws在github地址](https://link.juejin.cn/?target=https%3A%2F%2Fgithub.com%2Fwebsockets%2Fws "https://github.com/websockets/ws")

WebSocket协议定义了两种URL方案，WS和WSS分别代表了客户端和服务端之间未加密和加密的通信。WS(WebSocket)类似于Http URL，而WSS（WebSocket Security）URL 表示连接是基于安全传输层（TLS/SSL）和https的连接是同样的安全机制。

### 2.服务端server.js配置

在项目里面新建一个server.js，创建服务，指定8181端口，将收到的消息log出来。

```
var WebSocketServer = require('ws').Server,
wss = new WebSocketServer({ port: 8181 });
wss.on('connection', function (ws) {
    console.log('client connected');
    ws.on('message', function (message) {
        console.log(message);
    });
});

```

服务端完整代码如下：

```
var WebSocket = require('ws').Server;

let wss = new WebSocket({
    port:8181
})
// wss 继承eventEmitter

wss.on('connection',function(ws){
    //链接成功
    console.log('建立链接')
    // 监听客户端发送的信息
    ws.on('message',function(data){
        console.log(data);

        //单一：谁给我发， 我就给谁发
        ws.send('hello' + data )

        //群发 

    })

    ws.on('close',function(){
        console.log('关闭链接')
    })
});

```

### 3.客户端client.html建立WebSocket链接

我们需要通过调用WebSocket构造函数来创建一个WebSocket连接，构造函数会返回一个WebSocket实例，可以用来监听事件。这些事件会告诉你什么时候连接建立，什么时候消息到达，什么时候连接关闭了，以及什么时候发生了错误。

WebSocket的构造函数需要一个URL参数和一个可选的协议参数（一个或者多个协议的名字），协议的参数例如XMPP、SOAP（Simple Object Access Protocol）或者自定义协议。而URL参数需要以WS://或者WSS://开头，例如：ws://[www.websocket.org](https://link.juejin.cn/?target=http%3A%2F%2Fwww.websocket.org "http://www.websocket.org")

在页面上建立一个WebSocket的连接。用send方法发送消息。

```
var ws = new WebSocket("ws://localhost:8181");
    ws.onopen = function (e) {
        console.log('Connection to server opened');
    }
    function sendMessage() {
        ws.send($('#message').val());
    }

```

具体完整代码如下：

```
  <input class="form-control" type="text" name="message" id="message"
            placeholder="Type text to echo in here" value="" />
            <button type="button" id="send" class="btn btn-primary" onclick="sendMessage();">
            Send!
        </button>
<script>
         //原生的 ie9不支持
        var ws = new WebSocket("ws://localhost:8181");
            ws.onopen = function (e) {
                console.log('Connection to server opened');
                let msg = {type:'test',id:1,info:'laney'}
                ws.send(JSON.stringify(msg));
                console.log("sened a laney");
            }
            function sendMessage() {
                var message = document.getElementById('message');
                // ws.send(message.value); //给后端发送信息

                ws.send(JSON.stringify({type:'test1',id:1,info:message.value}))
            }
            //收到服务端的请求
            ws.onmessage = function (e) {
                console.log(e.data);
            }

            </script>  
```

运行之后如下，服务端即时获得客户端的消息。

## 七、websocket服务端基于socket.io的搭建

socket.io就是众多websocket库中的一种，它并不像其它库那样简单地实现了一下websocket，而是在websocket外面包裹了厚厚的一层。普通的websocket（例如ws库）只需要服务端就够了，socket.io自定义了一种基于websocket的协议，所以socket.io的服务端和客户端必须配套。简言之，如果服务端使用socket.io，那么客户端就没得选了，必然也用socket.io的客户端。

-   socket.io 是 基于engine.io 进行封装的库
-   socket.io 是基于 Websocket 的Client-Server 实时通信库。
-   socket.io 底层使用engine.io 封装了一层协议。

socket.io提供了一个房间(Namespace)概念。当客户端创建一个新的长连接时，就会分配一个新的Namespace进行区分。

### 7.1 socket.io事件

#### 7.1.1 socket.io服务器的系统事件

io.on('connection', callback): 有新Socket连接建立时 socket.on('message', callback): 当有socket..send()方法发送完信息后触发 socket.on('disconnect', callback): 当连接断开时触发

#### 7.1.2 客户端的系统事件

socket.io.on(‘open’, callback): 当socket客户端开启与服务器的新连接时触发 socket.io.on(‘connect’, callback):当socket客户端连接到服务器后触发 socket.io.on(‘connect\_timeout’, callback):当socket客户端与服务器连接超时后触发 socket.io.on(‘connec\_errort’, callback):当socket客户端连接服务器失败时触发 socket.io.on(‘connec\_attemptt’, callback):当socket客户端尝试重新连接到服务器后触发 socket.io.on(‘reconnect’, callback):当socket客户端重新连接到服务器后触发 socket.io.on(‘reconnect\_error’, callback):当socket客户端重新连接服务器失败后触发 socket.io.on(‘reconnect\_fail’, callback):当socket客户端重新连接到服务器失败后触发 socket.io.on(‘close’, callback):当socket客户端关闭与服务器的连接后触发

### 7.2 原生 nodejs 结合 Socket.io 实现服务器和 客户端的相互通信

1、 安装 Socket.io 2、 写原生的 JS，搭建一个服务器，server 创建好之后，创建一个 io 对象。 3、 服务器端通过 emit 广播，通过 on 接收广播 4、 客户端端通过 emit 广播，通过 on 接收广播

WebSocket protocol是HTML5一种新的协议。它实现了浏览器与服务器全双工通信，同时允许跨域通讯，是server push技术的一种很好的实现。我们使用Socket.io，它很好地封装了webSocket接口，提供了更简单、灵活的接口，也对不支持webSocket的浏览器提供了向下兼容。

#### 7.2.1 为什么需要 WebSocket

了解计算机网络协议的人，应该都知道：HTTP 协议是一种无状态的、无连接的、单向的应用层协议。它采用了请求/响应模型。通信请求只能由客户端发起，服务端对请求做出应答处理。

这种通信模型有一个弊端：HTTP 协议无法实现服务器主动向客户端发起消息。

#### 7.2.2 具体步骤和使用

**1、 安装 Socket.io**

网址：[socket.io/](https://link.juejin.cn/?target=http%3A%2F%2Fsocket.io%2F "http://socket.io/")

```
npm install socket.io 
或者 yarn add  socket.io
```

**2、搭建一个server 服务器， 创建一个 io 对象。**

```
var http = require('http');
var path = require('path');
var fs = require('fs');

//引入socket.io
var socket = require('socket.io');

var app = http.createServer(function(req,res){
     //加载静态页面

    fs.readFile(path.resolve(__dirname,'app.html'),function(err,data){
        if(req.url=="/"){ 
            res.writeHead(200,{"Content-Type":"text/html;charset='utf-8'"});
            res.end(data);
        }
    })

})

// socket 与 http服务建立连接
var io = socket(app);

// 监听连接事件 
io.on('connection',function(sock){
    console.log(sock)
    console.log('服务器建立连接了');
})

app.listen(3000,'127.0.0.1');
```

socket 与 http服务建立连接后，你就会发现，[http://127.0.0.1:3000/socket.io/socket.io.js](https://link.juejin.cn/?target=http%3A%2F%2F127.0.0.1%3A3000%2Fsocket.io%2Fsocket.io.js "http://127.0.0.1:3000/socket.io/socket.io.js") 就是一个 js 文件 的地址了。

现在需要制作一个客户端的 index 页面，这个页面中，必须引用秘密 js 文件。调用 io 函数，取得 socket 对象。

index.html

```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>客户端</title>
    <script src="http://localhost:3000/socket.io/socket.io.js"></script>
</head>
<body>
    
    <script>
      var socket = io('http://localhost:3000/');  /*和服务器建立连接*/
    </script>
</body>
</html>

```

这个时候会在终端打印出 '一个客户端与服务器建立连接'

**3、服务器端通过 emit 广播，通过 on 接收广播**

```

//引入socket.io
var socket = require('socket.io');

// socket 与 http服务建立连接
var io = socket(app);

// 监听连接事件 
io.on('connection',function(sock){
    console.log('一个客户端与服务器建立连接了');

    //服务器获取客户端广播的数据
    sock.on('addcart', function(data){
        console.log(data);

          //服务器给客户端发送数据
            //socket.emit();   /*谁给我发信息我把信息广播给谁*/
            //io.emit() ;   /*群发  给所有连接服务器的客户都广播数据*/

        socket.emit('to-client','我是服务器的数据'+data.client);

        //io.emit('to-client','我是服务器的数据'+data.client);

    })

    sock.on('disconnect',function(){
        console.log('断开连接了');
    })

})
```

每一个连接上来的用户，都有一个 socket。 由于我们的 emit 语句，是 socket.emit()发 出的，所以指的是向这个客户端发出语句。

广播，就是给所有当前连接的用户发送信息：

```
io.emit('to-client','我是服务器的数据'+data.client);
 /*群发  给所有连接服务器的客户都广播数据*/

```

**4\. 客户端端通过 emit 广播，通过 on 接收广播**

```
 <button id="button">给服务器发送数据</button>
    <script src="http://localhost:3000/socket.io/socket.io.js"></script>
    <script>
      var socket = io('http://localhost:3000/');  /*和服务器建立连接*/

      socket.on('connect',function(){
            console.log('客户端和服务端建立连接了');
      })

      socket.on('disconnect',function(){
            console.log('客户端和服务端断开连接了');
      })


      var btn=document.getElementById('button');
      btn.onclick= function () {
                //客户端给服务器发送数据
                socket.emit('addcart',{
                    client:'我是客户端的数据'
                })
        }

       //监听服务器的广播
      socket.on('to-client',function(data){
         console.log('服务端说:'+data)
       })

    </script>

```

### 7.3 Express 结合 Socket.io 实现服务器和客户 端的相互通信

第一个目标是建立一个简单的HTML网页，提供表单和消息列表。

```
var app = require('express')();
//创建http server ,  createServer 与 Server作用一样都是创建server
var http = require('http').createServer(app);
//var http = require('http').Server(app);

app.get('/', (req, res) => {
  res.send('<h1>Hello world</h1>');
});

http.listen(3000, () => {
  console.log('listening on *:3000');
});

```

Express将app初始化为可以提供给HTTP服务器的函数处理程序 我们定义了一个路由处理程序，当我们访问我们的网站主页时，它会被调用。

我们让http服务器监听端口3000。

#### 7.3.1 引入 Socket.IO

npm install socket.io

注意：socket.io基于http服务(socket.io的参数就是http server)

```
var app = require('express')();
var http = require('http').createServer(app);
var io = require('socket.io')(http);

app.get('/', (req, res) => {
  res.sendFile(__dirname + '/index.html');
});

io.on('connection', (socket) => {
  console.log('a user connected');
});

http.listen(3000, () => {
  console.log('listening on *:3000');
});
```

#### 7.3.2 写socket的代码

```
//3、写socket的代码
io.on('connection', function (socket) {
      console.log('a user connected');
      socket.on('message',function(key){
          console.log(key);
  
         //io.emit  广播
        //socket.emit  谁给我发的信息我回返回给谁
          //io.emit('servermessage',data);   /*服务器给客户端发送数据*/

         //   机器人聊天
        //   console.log(robot[key])
        //   socket.emit('servermessage',robot[key]);

          io.emit('servermessage',key.msg);

      })
  });
```

#### 7.3.3 群聊

io.emit 广播 只要接收到客户端的信息， 就把这个信息发送给连接到这个服务的所有客户端用户

io.emit('servermessage',data); /_服务器给客户端发送数据_/

#### 7.3.4 机器人聊天

socket.emit 谁给我发的信息我回返回给谁 具体代码如上：

```
socket.emit('servermessage',robot[key]);
```

#### 7.3.5 客户端 代码

```
  <ul id="messages"> </ul>
    <form >
      <input id="msg" autocomplete="off" /><button id="send" >Send</button>
    </form>
    <script src="https://code.jquery.com/jquery-3.4.1.min.js"></script>
    <script src="/socket.io/socket.io.js"></script>
<script>
  var socket = io();

     //群聊功能--聊天室
     $('#send').click(function(e){
         e.preventDefault();
            var msg=$('#msg').val();
            // socket.emit('message',msg);  /*客户端给服务器发送数据*/

            // 带上自己的名字
            socket.emit('message',{
                name:'laney',
                msg:msg
            });  /*客户端给服务器发送数据*/


        })

   //接受服务器返回的数据
   socket.on('servermessage',function(data){
        console.log(data)
        $('#messages').append(`<li>${data}<li>`)
    })

</script>

```

参考：

```
https://github.com/websockets/ws
https://www.cnblogs.com/chyingp/p/6072338.html
https://www.cnblogs.com/tzyy/p/5124256.html
```
