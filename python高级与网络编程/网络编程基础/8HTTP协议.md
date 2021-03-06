# 8 HTTP协议

## 8.1 HTTP简介

HTTP协议（HyperText Transfer Protocol，超文本传输协议）是用于从WWW服务器传输超文本到本地浏览器的传输协议。它可以使浏览器更加高效，使网络传输减少。它不仅保证计算机正确快速地传输超文本文档，还确定传输文档中的哪一部分，以及哪部分内容首先显示\(如文本先于图形\)等。

HTTP是客户端浏览器或其他程序与Web服务器之间的应用层通信协议。在Internet上的Web服务器上存放的都是超文本信息，客户机需要通过HTTP协议传输所要访问的超文本信息。HTTP包含命令和传输信息，不仅可用于Web访问，也可以用于其他因特网/内联网应用系统之间的通信，从而实现各类应用资源超媒体访问的集成。

我们在浏览器的地址栏里输入的网站地址叫做URL \(Uniform Resource Locator，统一资源定位符\)。就像每家每户都有一个门牌地址一样，每个网页也都有一个Internet地址。当我们在浏览器的地址框中输入一个URL或是单击一个超级链接时，URL就确定了要浏览的地址。浏览器通过超文本传输协议\(HTTP\)，将Web服务器上站点的网页代码提取出来，并翻译成漂亮的网页。

也就是说：

* **HTML是一种用来定义网页的文本，会HTML，就可以编写网页；**
* HTML默认端口80；
* **HTTP是在网络上传输HTML的协议，用于浏览器和服务器的通信。**

## 8.2 HTTP分析

在举例子之前，我们需要安装Google的[Chrome浏览器](http://www.google.com/intl/zh-CN/chrome/)。为什么要使用Chrome浏览器而不是IE呢？因为IE实在是太慢了，并且，IE对于开发和调试Web应用程序完全是一点用也没有。我们需要在浏览器很方便地调试我们的Web应用，而Chrome提供了一套完整地调试工具，非常适合Web开发。安装好Chrome浏览器后，打开Chrome，在菜单中选择“视图”，“开发者”，“开发者工具”，就可以显示开发者工具：

![](/assets/http1.png)

`Elements`显示网页的结构，`Network`显示浏览器和服务器的通信。我们点`Network`，确保第一个小红灯亮着，Chrome就会记录所有浏览器和服务器之间的通信：

![](/assets/http2.png)

### 8.2.1 浏览器请求分析

当我们在地址栏输入www.sina.com时，浏览器将显示新浪的首页。在这个过程中，浏览器都干了哪些事情呢？通过Network的记录，我们就可以知道。在Network中，找到www.sina.com那条记录，点击，右侧将显示Request Headers，点击右侧的view source，我们就可以看到浏览器发给新浪服务器的请求：

![](/assets/http3.png)![](/assets/http5.png)

最主要的头两行分析如下，第一行：

```
    GET / HTTP/1.1
```

GET表示一个读取请求，将从服务器获得网页数据，/表示URL的路径，URL总是以/开头，/就表示首页，最后的HTTP/1.1指示采用的HTTP协议版本是1.1。目前HTTP协议的版本就是1.1，但是大部分服务器也支持1.0版本，主要区别在于1.1版本允许多个HTTP请求复用一个TCP连接，以加快传输速度。

从第二行开始，每一行都类似于Xxx: abcdefg：

```
    Host: www.sina.com
```

表示请求的域名是www.sina.com。如果一台服务器有多个网站，服务器就需要通过Host来区分浏览器请求的是哪个网站。

### 8.2.2 服务器响应分析 {#22-服务器响应}

继续找到Response Headers，点击view source，显示服务器返回的原始响应数据：![](/assets/http6.png)

HTTP响应分为Header和Body两部分（Body是可选项），我们在Network中看到的Header最重要的几行如下：

```
    HTTP/1.1 200 OK
```

200表示一个成功的响应，后面的OK是说明。

如果返回的不是200，那么往往有其他的功能，例如

* 失败的响应有404 Not Found：网页不存在
* 500 Internal Server Error：服务器内部出错
* ...等等...

```
    Content-Type: text/html
```

Content-Type指示响应的内容，这里是text/html表示HTML网页。

> `请注意，浏览器就是依靠Content-Type来判断响应的内容是网页还是图片，是视频还是音乐。浏览器并不靠URL来判断响应的内容，所以，即使URL是http://www.baidu.com/meimei.jpg，它也不一定就是图片。`

HTTP响应的Body就是HTML源码，我们在菜单栏选择“视图”，“开发者”，“查看网页源码”就可以在浏览器中直接查看HTML源码：![](/assets/http7.png)

### 8.2.3 浏览器解析过程

跟踪了新浪的首页，我们来总结一下HTTP请求的流程：

**步骤1：浏览器首先向服务器发送HTTP请求**，请求包括：  
方法：GET还是POST，GET仅请求资源，POST会附带用户数据；  
路径：/full/url/path；  
域名：由Host头指定：Host: www.sina.com.cn  
以及其他相关的Header；  
**如果是POST，那么请求还包括一个Body，包含用户数据。**

**步骤2：服务器向浏览器返回HTTP响应**，响应包括：  
响应代码：200表示成功，3xx表示重定向，4xx表示客户端发送的请求有错误，5xx表示服务器端处理时发生了错误；  
响应类型：由Content-Type指定；  
以及其他相关的Header；

**通常服务器的HTTP响应会携带内容，也就是有一个Body，包含响应的内容，网页的HTML源码就在Body中。**

**步骤3：如果浏览器还需要继续向服务器请求其他资源，**比如图片，就再次发出HTTP请求，重复步骤1、2。当浏览器读取到新浪首页的HTML源码后，它会解析HTML，显示页面，然后，根据HTML里面的各种链接，再发送HTTP请求给新浪服务器，拿到相应的图片、视频、Flash、JavaScript脚本、CSS等各种资源，最终显示出一个完整的页面。所以我们在Network下面能看到很多额外的HTTP请求。

Web采用的HTTP协议采用了非常简单的请求-响应模式，从而大大简化了开发。当我们编写一个页面时，我们只需要在HTTP请求中把HTML发送出去，不需要考虑如何附带图片、视频等，浏览器如果需要请求图片和视频，它会发送另一个HTTP请求，因此，一个HTTP请求只处理一个资源。

HTTP协议同时具备极强的扩展性，虽然浏览器请求的是http://www.sina.com.cn/ 的首页，但是新浪在HTML中可以链入其他服务器的资源，比如&lt;imghttp://i1.sinaimg.cn/home/2013/1008/U8455P30DT20131008135420.png"&gt;， 从而将请求压力分散到各个服务器上，并且，一个站点可以链接到其他站点，无数个站点互相链接起来，就形成了World src=" Wide Web，简称WWW。

![](/assets/http8.png)

## 8.3 HTTP格式

**每个HTTP请求和响应都遵循相同的格式，一个HTTP包含Header和Body两部分，其中Body是可选的**。HTTP协议是一种文本协议，所以，它的格式也非常简单。

### 8.3.1 HTTP GET请求的格式：

```
GET /path HTTP/1.1
Header1: Value1
Header2: Value2
Header3: Value3
```

每个Header一行一个，换行符是**\r\n**。

### 8.3.2 HTTP POST请求的格式：

```
POST /path HTTP/1.1
Header1: Value1
Header2: Value2
Header3: Value3

body data goes here...
```

当遇到连续两个**\r\n**时，Header部分结束，后面的数据全部是Body。

### 8.3.3 HTTP响应的格式：

```
200 OK HTTP/1.1
Header1: Value1
Header2: Value2
Header3: Value3

body data goes here...
```

HTTP响应如果包含body，也是通过**\r\n\r\n**来分隔的。请再次注意，Body的数据类型由Content-Type头来确定，如果是网页，Body就是文本，如果是图片，Body就是图片的二进制数据。

当存在Content-Encoding时，Body数据是被压缩的，最常见的压缩方式是gzip，所以，看到Content-Encoding: gzip时，需要将Body数据先解压缩，才能得到真正的数据。压缩的目的在于减少Body的大小，加快网络传输。

## 8.4 TCP短连接与长连接

### 8.4.1 TCP短连接

模拟一种TCP短连接的情况:

1. client 向 server 发起连接请求
2. server 接到请求，双方建立连接
3. client 向 server 发送消息
4. server 回应 client
5. 一次读写完成，此时双方任何一个都可以发起 close 操作

在步骤5中，一般都是 client 先发起 close 操作。当然也不排除有特殊的情况。

从上面的描述看，短连接一般只会在 client/server 间传递一次读写操作！

### 8.4.2 TCP长连接

再模拟一种长连接的情况:

1. client 向 server 发起连接
2. server 接到请求，双方建立连接
3. client 向 server 发送消息
4. server 回应 client
5. 一次读写完成，连接不关闭
6. 后续读写操作...
7. 长时间操作之后client发起关闭请求

### 8.4.3. TCP长/短连接操作过程

### 8.4.3.1 短连接的操作步骤是： {#31-短连接的操作步骤是：}

建立连接——数据传输——关闭连接...建立连接——数据传输——关闭连接

![](/assets/http12.png)

### 8.4.3.2 长连接的操作步骤是： {#32-长连接的操作步骤是：}

建立连接——数据传输...（保持连接）...数据传输——关闭连接

![](/assets/http13.png)

## 8.4.4 TCP长/短连接的优点和缺点 {#4-tcp长短连接的优点和缺点}

长连接就像我们的公交卡、地铁卡，一次购买多次乘车；短连接就像我们的单程票，每次乘车都要购票。

* 长连接可以省去较多的TCP建立和关闭的操作，减少浪费，节约时间。  
  对于频繁请求资源的客户来说，较适用长连接。

* client与server之间的连接如果一直不关闭的话，会存在一个问题，  
  随着客户端连接越来越多，server早晚有扛不住的时候，这时候server端需要采取一些策略，如关闭一些长时间没有读写事件发生的连接，这样可以避免一些恶意连接导致server端服务受损；如果条件再允许就可以以客户端机器为颗粒度，限制每个客户端的最大长连接数，这样可以完全避免某个蛋疼的客户端连累后端服务。

* 短连接对于服务器来说管理较为简单，存在的连接都是有用的连接，不需要额外的控制手段。

* 但如果客户请求频繁，将在TCP的建立和关闭操作上浪费时间和带宽。

## 8.4.5 TCP长/短连接的应用场景 {#5-tcp长短连接的应用场景}

* 长连接多用于操作频繁，点对点的通讯，而且连接数不能太多情况。每个TCP连接都需要三次握手，这需要时间，如果每个操作都是先连接;再操作的话那么处理速度会降低很多，所以每个操作完后都不断开;再次处理时直接发送数据包就OK了，不用建立TCP连接。

  例如：数据库的连接用长连接，如果用短连接频繁的通信会造成socket错误，而且频繁的socket 创建也是对资源的浪费。

* 小的WEB网站的http服务一般都用短链接，因为长连接对于服务端来说会耗费一定的资源 。而像WEB网站这么频繁的成千上万甚至上亿客户端的连接用短连接会更省一些资源。如果用长连接，同时有成千上万的用户，如果每个用户都占用一个连接的话，那可想而知吧。所以并发量大，但每个用户无需频繁操作情况下需用短连好。

* 对于中大型WEB网站一般都采用长连接，好处是响应用户时间更短，用户体验更好，虽然更耗硬件资源一些，但这都不是事儿。



## 8.5 python程序模拟浏览器

### 需求实现：

通过python的socket来模拟浏览器请求服务和解析网页的过程，来实现百度主页网页下载。

### 根据需求绘制流程图

![](/assets/http15.png)

我们打开百度主页会发现浏览器的请求信息为：GET / HTTP/1.1 Host: www.baidu.com因此我们将此数据发送给百度，这就模拟了浏览器的网页请求。接下来我们通过recv接收百度服务器的响应信息：在之前的小节中我们已经知道，响应头和响应体之间有一个空格，我们可以通过此来区分响应头和响应体，在这之前的为响应头，之后的为响应体。响应体即网页内容，通过文件写操作保存至本地。

![](/assets/http18.png)

### 完整代码

```py
'''net08_http_browser.py'''
import socket


tcp_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
tcp_sock.connect(('www.baidu.com', 80))  # http默认端口80

# 模拟浏览器GET内容
request_line = "GET / HTTP/1.1\r\n"  # 请求行
request_header = "Host: www.baidu.com\r\n"  # 请求行

# 请求数据
request_data = request_line + request_header + '\r\n'
# 发送请求
tcp_sock.send(request_data.encode('utf-8'))

# 收取服务器响应数据
resp_data = tcp_sock.recv(4096)

# 解析响应数据
index = resp_data.find('\r\n\r\n')  # 找到空行,空行后面就是响应body，即网页内容
resp_headers = resp_data[:index + 1]  # 响应头和响应行
html_data = resp_data[index + 4:]  # 响应body
print(resp_headers)  # 显示服务器响应信息

# 保存网页到到本地文件baidu.html
with open('baidu.html', 'wb') as html_file:
    html_file.write(html_data.encode())
```

### 实现结果

![](/assets/http17.png)

运行代码之后，我们会发现打印显示的响应头信息和浏览器的信息时一样的。也可以看到文件夹中多了一个baidu.html。用浏览器打开该文件，和浏览器直接访问百度主页的内容一样。说明我们成功的模拟了浏览器实现网页请求和获取。

