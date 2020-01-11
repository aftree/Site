# HTTP



## HTTP简介 <a id="HTTP&#x7B80;&#x4ECB;"></a>

HTTP（HyperText Transfer Protocol）即超文本传输协议，是一种详细规定了浏览器和万维网服务器之间互相通信的规则，它是万维网交换信息的基础，它允许将HTML（超文本标记语言）文档从Web服务器传送到Web浏览器。

HTTP协议目前最新版的版本是1.1，HTTP是一种**无状态的协议**，**无状态是指Web浏览器与Web服务器之间不需要建立持久的连接**，这意味着当一个客户端向服务器端发出请求，然后Web服务器返回响应（Response），连接就被关闭了，在服务器端不保留连接的有关信息。也就是说，HTTP请求只能由客户端发起，而服务器不能主动向客户端发送数据。

HTTP是一个基于TCP/IP通信协议来传递数据（HTML 文件, 图片文件, 查询结果等）。

### HTTP工作原理： <a id="HTTP&#x5DE5;&#x4F5C;&#x539F;&#x7406;&#xFF1A;"></a>

HTTP协议工作于客户端-服务端架构上。浏览器作为HTTP客户端通过URL向HTTP服务端即WEB服务器发送所有请求。

Web服务器有：Apache服务器，IIS服务器（Internet Information Services）等。

Web服务器根据接收到的请求后，向客户端发送响应信息。

HTTP默认端口号为80，但是你也可以改为8080或者其他端口。

**HTTP三点注意事项：**

* HTTP是无连接：无连接的含义是限制每次连接只处理一个请求。服务器处理完客户的请求，并收到客户的应答后，即断开连接。采用这种方式可以节省传输时间。
* HTTP是媒体独立的：这意味着，只要客户端和服务器知道如何处理的数据内容，任何类型的数据都可以通过HTTP发送。客户端以及服务器指定使用适合的MIME-type内容类型。
* HTTP是无状态：HTTP协议是无状态协议。无状态是指协议对于事务处理没有记忆能力。缺少状态意味着如果后续处理需要前面的信息，则它必须重传，这样可能导致每次连接传送的数据量增大。另一方面，在服务器不需要先前信息时它的应答就较快。

## HTTP请求与响应 <a id="HTTP&#x8BF7;&#x6C42;&#x4E0E;&#x54CD;&#x5E94;"></a>

HTTP遵循请求（Request）/应答（Response）模型，Web浏览器向Web服务器发送请求时，Web服务器处理请求并返回适当的应答。

### HTTP请求： <a id="HTTP&#x8BF7;&#x6C42;&#xFF1A;"></a>

复制

|  |  |
| :--- | :--- |


HTTP请求包括三部分，分别是请求行（请求方法）、请求头（消息报头）和请求正文。

HTTP请求第一行为请求行，由三部分组成，第一部分说明了该请求时POST请求，第二部分是一个斜杠（/login.php），用来说明请求是该域名根目录下的login.php，第三部分说明使用的是HTTP1.1版本。

HTTP请求第二行至空白行为请求头（也被称为消息头）。其中，HOST代表请求主机地址，User-Agent代表浏览器的标识，请求头由客户端自行设定。

HTTP请求第三行为请求正文，请求正文是可选的，它最常出现在POST请求方式中。

#### HTTP请求方法： <a id="HTTP&#x8BF7;&#x6C42;&#x65B9;&#x6CD5;&#xFF1A;"></a>

根据 HTTP 标准，HTTP 请求可以使用多种请求方法。

HTTP1.0 定义了三种请求方法： GET, POST 和 HEAD方法。

HTTP1.1 新增了五种请求方法：OPTIONS、PUT、PATCH、DELETE、TRACE 和 CONNECT 方法。

| 序号 | 方法 | 描述 |
| :--- | :--- | :--- |
| 1 | GET | 请求指定的页面信息，并返回实体主体。 |
| 2 | HEAD | 类似于 GET 请求，只不过返回的响应中没有具体的内容，用于获取报头 |
| 3 | POST | 向指定资源提交数据进行处理请求（例如提交表单或者上传文件）。数据被包含在请求体中。POST 请求可能会导致新的资源的建立和/或已有资源的修改。 |
| 4 | PUT | 从客户端向服务器传送的数据取代指定的文档的内容。 |
| 5 | DELETE | 请求服务器删除指定的页面。 |
| 6 | CONNECT | HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器。 |
| 7 | OPTIONS | 允许客户端查看服务器的性能。 |
| 8 | TRACE | 回显服务器收到的请求，主要用于测试或诊断。 |
| 8 | PATCH | 是对 PUT 方法的补充，用来对已知资源进行局部更新 。 |

**GET、POST、HEAD、PUT请求：**

* GET：GET方法用于获取请求页面的指定信息。如果请求资源为动态脚本（非HTML），那么返回文本是Web容器解析后的HTML源代码。**GET请求没有消息主体**，因此在消息头后的空白行是没有其他数据。
* POST：POST方法也与GET方法相似，但最大的区别在于，GET方法没有请求内容，而POST是有请求内容的。
* HEAD：这个请求的功能与GET请求相似，不同之处在于服务器不会再其响应中返回消息主体，因此，这种方法可用于检查某一资源在向其提交GET请求前是否存在。
* PUT：PUT方法用于请求服务器把请求中的实体存储在请求资源下，如果请求资源已经在服务器中存在，那么将会用此请求中的数据替换原先的数据。向服务器上传指定的资源。

### HTTP响应： <a id="HTTP&#x54CD;&#x5E94;&#xFF1A;"></a>

复制

|  |  |
| :--- | :--- |


HTTP响应的第一行为响应行，其中有HTTP版本（HTTP/1.1）、状态码（200）以及消息“OK”。

第二行至末尾的空白行为响应头，由服务器向客户端发送。

消息头之后是响应正文，是服务器向客户端发送的HTML数据。

### HTTP消息头： <a id="HTTP&#x6D88;&#x606F;&#x5934;&#xFF1A;"></a>

请求头：请求头只出现在HTTP请求中，请求报头允许客户端向服务端传递请求的附加信息和客户端自身信息。

响应头：响应头是服务器根据请求向客户端发送的HTTP头。

#### HTTP请求头： <a id="HTTP&#x8BF7;&#x6C42;&#x5934;&#xFF1A;"></a>

* Host 请求报头域主要用于指定被请求资源的Internet主机和端口。
* User-Agent 请求报头域允许客户端将它的操作系统、浏览器和其他属性告诉服务器。
* Referer 包含一个URL，代表当前访问URL的上一个URL，也就是说，用户是从什么地方来到本页面。当前请求的原始URL地址。
* Cookie 是非常重要的请求头，常用来表示请求者的身份等。
* Accept 这个消息头用于告诉服务器客户端愿意接受那些内容，比如图像类，办公文档格式等等。

#### HTTP响应头信息： <a id="HTTP&#x54CD;&#x5E94;&#x5934;&#x4FE1;&#x606F;&#xFF1A;"></a>

| 应答头 | 说明 |
| :--- | :--- |
| Allow | 服务器支持哪些请求方法（如GET、POST等）。 |
| Content-Encoding | **文档的编码（Encode）方法**。只有在解码之后才可以得到Content-Type头指定的内容类型。利用gzip压缩文档能够显著地减少HTML文档的下载时间。 |
| Content-Length | **表示内容长度。** |
| Content-Type | **表示后面的文档属于什么MIME类型。**Servlet默认为text/plain，但通常需要显式地指定为text/html。 |
| Date | **当前的GMT时间。** |
| Expires | 应该在什么时候认为文档已经过期，从而不再缓存它？ |
| Last-Modified | **文档的最后改动时间。**客户可以通过If-Modified-Since请求头提供一个日期，该请求将被视为一个条件GET，只有改动时间迟于指定时间的文档才会返回，否则返回一个304（Not Modified）状态。 |
| Location | **表示客户应当到哪里去提取文档。**Location通常不是直接设置的，而是通过HttpServletResponse的sendRedirect方法，该方法同时设置状态代码为302。 |
| Refresh | **表示浏览器应该在多少时间之后刷新文档，以秒计**。注意Refresh头不属于HTTP 1.1正式规范的一部分，而是一个扩展，但Netscape和IE都支持它。 |
| Server | 服务器名字。Servlet一般不设置这个值，而是由Web服务器自己设置。 |
| Set-Cookie | 设置和页面关联的Cookie。 |
| WWW-Authenticate | 客户应该在Authorization头中提供什么类型的授权信息？在包含401（Unauthorized）状态行的应答中这个头是必需的。 |

### 拦截HTTP请求的分析点： <a id="&#x62E6;&#x622A;HTTP&#x8BF7;&#x6C42;&#x7684;&#x5206;&#x6790;&#x70B9;&#xFF1A;"></a>

![&#x62E6;&#x622A;HTTP&#x8BF7;&#x6C42;&#x5206;&#x6790;&#x70B9;](https://cshihong.github.io/2019/06/19/HTTP%E5%8D%8F%E8%AE%AE%E5%88%86%E6%9E%90/%E6%8B%A6%E6%88%AAHTTP%E8%AF%B7%E6%B1%82%E5%88%86%E6%9E%90%E7%82%B9.png)

## HTTP状态与会话 <a id="HTTP&#x72B6;&#x6001;&#x4E0E;&#x4F1A;&#x8BDD;"></a>

### HTTP状态码： <a id="HTTP&#x72B6;&#x6001;&#x7801;&#xFF1A;"></a>

当浏览者访问一个网页时，浏览者的浏览器会向网页所在服务器发出请求。当浏览器接收并显示网页前，此网页所在的服务器会返回一个包含HTTP状态码的信息头（server header）用以响应浏览器的请求。

HTTP状态码的英文为HTTP Status Code。

**五种状态码：**

* 1xx：信息提示，表示请求已被成功接收，继续处理。
* 2xx：请求被成功提交。
* 3xx：客户端被重定向到其他资源。
* 4xx：客户端错误状态码，格式错误或者不存在资源。
* 5xx：描述服务器内部错误。

**常见的状态码描述如下：**

* 200：客户端请求成功，是最常见的状态。
* 302：重定向。
* 404：请求资源不存在，是最常见的状态。
* 400：客户端请求有语法错误，不能被服务器所理解。
* 401：请求未经授权。
* 403：服务器收到请求，但是拒绝提供服务。
* 500：服务器内部错误，是最常见的状态。
* 503：服务器当前不能处理客户端的请求。

### 会话与会话状态简介： <a id="&#x4F1A;&#x8BDD;&#x4E0E;&#x4F1A;&#x8BDD;&#x72B6;&#x6001;&#x7B80;&#x4ECB;&#xFF1A;"></a>

WEB应用中的会话是指一个客户端浏览器与WEB服务器之间连续发生的一系列请求和响应过程。

WEB应用的会话状态是指WEB服务器与浏览器在会话过程中产生的状态信息，借助会话状态，WEB服务器能够把属于同一会话中的一系列的请求和响应过程关联起来。

#### **如何实现有状态的会话：** <a id="&#x5982;&#x4F55;&#x5B9E;&#x73B0;&#x6709;&#x72B6;&#x6001;&#x7684;&#x4F1A;&#x8BDD;&#xFF1A;"></a>

某个用户从网站的登录页面登入后，在进入购物页面购物时，负责处理购物请求的服务器程序必须知道处理上一次请求的程序所得到的用户信息。

HTTP协议是一种无状态的协议，WEB服务器本身不能识别出哪些请求是同一个浏览器发出的 ，浏览器的每一次请求都是完全孤立的。

WEB服务器端程序要能从大量的请求消息中区分出哪些请求消息属于同一个会话，即能识别出来自同一个浏览器的访问请求，这需要浏览器对其发出的每个请求消息都进行标识，属于同一个会话中的请求消息都附带同样的标识号，而属于不同会话的请求消息总是附带不同的标识号，这个标识号就称之为**会话ID（SessionID）**。

**会话ID可以通过一种称之为Cookie的技术在请求消息中进行传递，也可以作为请求URL的附加参数进行传递**。**会话ID是WEB服务器为每客户端浏览器分配的一个唯一代号**，它通常是在WEB服务器接收到某个浏览器的第一次访问时产生，并且随同响应消息一道发送给浏览器。

会话过程由WEB服务器端的程序开启，一旦开启了一个会话，服务器端程序就要为这个会话创建一个独立的存储结构来保存该会话的状态信息，同一个会话中的访问请求都可以且只能访问属于该会话的存储结构中的状态信息。

### Cookie技术： <a id="Cookie&#x6280;&#x672F;&#xFF1A;"></a>

Cookie是一种在客户端保持HTTP状态信息的技术，它好比商场发放的优惠卡。

Cookie是在浏览器访问WEB服务器的某个资源时，由WEB服务器在HTTP响应消息头中附带传送给浏览器的一片数据，WEB服务器传送给各个客户端浏览器的数据是可以各不相同的。

一旦WEB浏览器保存了某个Cookie，那么它在以后每次访问该WEB服务器时，都应在HTTP请求头中将这个Cookie回传给WEB服务器。

> Cookie技术最先被Netscape公司引入到 Navigator浏 览器中。
>
> 现在，绝大多 数浏览器都支持Cookie，或者至少兼容 Cookie技 术的使用。
>
> **Cookie是一小段文本信息，**伴随着用户请求和页 面在Web服务器和浏览器之间传递。Cookie包含每次 用户访问站点时Web应用程序都可以读取的信息。
>
> **Cookie只是一段文本，所以它只能保存字符串。**

WEB服务器通过在HTTP响应消息中增加Set-Cookie响应头字段将Cookie信息发送给浏览器，浏览器则通过在HTTP请求消息中增加Cookie请求头字段将Cookie回传给WEB服务器。

一个Cookie只能标识一种信息，它至少含有一个标识该信息的名称（NAME）和设置值（VALUE）。

一个WEB站点可以给一个WEB浏览器发送多个Cookie，一个WEB浏览器也可以存储多个WEB站点提供的Cookie。

浏览器一般只允许存放300个Cookie，每个站点最多存放20个Cookie，每个Cookie的大小限制为4KB。

**Cookie的传送过程示意图：**

![cookie&#x53D1;&#x9001;](https://cshihong.github.io/2019/06/19/HTTP%E5%8D%8F%E8%AE%AE%E5%88%86%E6%9E%90/cookie%E5%8F%91%E9%80%81.png)图：Cookie的传送过程示意图

#### Cookie功能特点： <a id="Cookie&#x529F;&#x80FD;&#x7279;&#x70B9;&#xFF1A;"></a>

* 存储于浏览器头部/传输于HTTP头部
* 写时带属性，读时无属性
* HTTP头中Cookie: user=bob; cart=books;
* 属性 name/value/expire/domain/path/httponly/secure/……
* 由三元组\[name,domain,path\]唯一确定cookie

#### Set-Cookie2响应字段： <a id="Set-Cookie2&#x54CD;&#x5E94;&#x5B57;&#x6BB5;&#xFF1A;"></a>

Set-Cookie2头字段用于指定WEB服务器向客户端传送的Cookie内容，但是按照Netscape规范实现Cookie功能的WEB服务器，使用的是Set-Cookie头字段，两者的语法和作用类似。

Set-Cookie2头字段中设置的cookie内容是具有一定格式的字符串，它必须以Cookie的名称和设置值开头，格式为“名称=值”，后面可以加上0个或多个以分号（;）和空格分隔的其它可选属性，属性格式一般为“属性名=值”。

> 举例：
>
> Set-Cookie2: user=hello; Version=1; Path=/

除了“名称=值”对必须位于最前面外，其它的可选属性的先后顺序可以任意。

**Cookie的名称只能由普通的英文ASCII字符组成**，浏览器不用关心和理解Cookie的值部分的意义和格式，只要WEB服务器能理解值部分的意义就行。

大多数现有的WEB服务器都是采用某种编码方式将值部分的内容编码成可打印的ASCII字符，RFC 2965规范中没有明确限定编码方式。

Cookie请求字段：

* Cookie请求头字段中的每个Cookie之间用逗号（,）或分号（;）分隔。
* 在Cookie请求头字段中除了必须有“名称=值”的设置外，还可以有Version、Path、Domain、Port等几个属性。
* 在Version、Path、Domain、Port等属性名之前，都要增加一个“$”字符作为前缀。
* Version属性只能出现一次，且要位于Cookie请求头字段设置值的最前面，如果需要设置某个Cookie信息的 Path、Domain、Port等属性，它们必须位于该Cookie信息的“名称=值”设置之后。•浏览器使用Cookie请求头字段将Cookie信息回送给WEB服务器。
* 多个Cookie信息通过一个Cookie请求头字段回送给WEB服务器。
* 浏览器根据下面的几个规则决定是否发送某个Cookie信息：
  * 请求的主机名是否与某个存储的Cookie的Domain属性匹配；
  * 请求的端口号是否在该Cookie的Port属性列表中；
  * 请求的资源路径是否在该Cookie的Path属性指定的目录及子目录中；
  * 该Cookie的有效期是否已过。
* Path属性指向子目录的Cookie排在Path属性指向父目录的Cookie之前。
* 举例：Cookie: $Version=1; Course=Java; $Path=/hello/lesson; Course=vc; $Path=/hello

#### Cookie的安全属性： <a id="Cookie&#x7684;&#x5B89;&#x5168;&#x5C5E;&#x6027;&#xFF1A;"></a>

* secure属性

  当设置为true时，表示创建的 Cookie 会被以安全的形式向服务器传输，也就是只能在 HTTPS 连接中被浏览器传递到服务器端进行会话验证，如果是 HTTP 连接则不会传递该信息，所以不会被窃取到Cookie 的具体内容。

* HttpOnly属性

  如果在Cookie中设置了”HttpOnly”属性，那么通过程序\(JS脚本、Applet等\)将无法读取到Cookie信息，这样能有效的防止XSS攻击。

**secure属性是防止信息在传递的过程中被监听捕获后信息泄漏，HttpOnly属性的目的是防止程序获取cookie后进行攻击。**

这两个属性并不能解决cookie在本机出现的信息泄漏的问题\(FireFox的插件FireBug能直接看到cookie的相关信息\)。

### Session技术： <a id="Session&#x6280;&#x672F;&#xFF1A;"></a>

#### 什么是Session？ <a id="&#x4EC0;&#x4E48;&#x662F;Session&#xFF1F;"></a>

使用Cookie和附加URL参数都可以将上一次请求的状态信息传递到下一次请求中，但是如果传递的状态信息较多，将极大降低网络传输效率和增大服务器端程序处理的难度。

Session技术是一种将会话状态保存在服务器端的技术 ，它可以比喻成是医院发放给病人的病历卡和医院为每个病人保留的病历档案的结合方式 。

客户端需要接收、记忆和回送 Session的会话标识号，Session可以且通常是借助Cookie来传递会话标识号。

#### Session的跟踪机制： <a id="Session&#x7684;&#x8DDF;&#x8E2A;&#x673A;&#x5236;&#xFF1A;"></a>

Servlet API规范中定义了一个HttpSession接口，HttpSession接口定义了各种管理和操作会话状态的方法。

HttpSession对象是保持会话状态信息的存储结构，一个客户端在WEB服务器端对应一个各自的HttpSession对象。

WEB服务器并不会在客户端开始访问它时就创建HttpSession对象，只有客户端访问某个能与客户端开启会话的Servlet程序时，WEB应用程序才会创建一个与该客户端对应的HttpSession对象。

WEB服务器为HttpSession对象分配一个独一无二的会话标识号，然后在响应消息中将这个会话标识号传递给客户端。客户端需要记住会话标识号，并在后续的每次访问请求中都把这个会话标识号传送给WEB服务器，WEB服务器端程序依据回传的会话标识号就知道这次请求是哪个客户端发出的，从而选择与之对应的HttpSession对象。

WEB应用程序创建了与某个客户端对应的HttpSession对象后，只要没有超出一个限定的空闲时间段，HttpSession对象就驻留在WEB服务器内存之中，该客户端此后访问任意的Servlet程序时，它们都使用与客户端对应的那个已存在的HttpSession对象。

HttpSession接口中专门定义了一个setAttribute方法来将对象存储到HttpSession对象中，还定义了一个getAttribute方法来检索存储在HttpSession对象中的对象，存储进HttpSession对象中的对象可以被属于同一个会话的各个请求的处理程序共享。

Session是实现网上商城的购物车的最佳方案，存储在某个客户Session中的一个集合对象就可充当该客户的一个购物车。

#### Session的超时管理： <a id="Session&#x7684;&#x8D85;&#x65F6;&#x7BA1;&#x7406;&#xFF1A;"></a>

WEB服务器无法判断当前的客户端浏览器是否还会继续访问，也无法检测客户端浏览器是否关闭，所以，即使客户已经离开或关闭了浏览器，WEB服务器还要保留与之对应的HttpSession对象。

随着时间的推移而不断增加新的访问客户端，WEB服务器内存中将会因此积累起大量的不再被使用的HttpSession对象，并将最终导致服务器内存耗尽。

WEB服务器采用“超时限制”的办法来判断客户端是否还在继续访问，如果某个客户端在一定的时间之内没有发出后续请求，WEB服务器则认为客户端已经停止了活动，结束与该客户端的会话并将与之对应的HttpSession对象变成垃圾。

如果客户端浏览器超时后再次发出访问请求，WEB服务器则认为这是一个新的会话的开始，将为之创建新的HttpSession对象和分配新的会话标识号。

会话的超时间隔可以在web.xml文件中设置，其默认值由Servlet容器定义。复制

|  |  |
| :--- | :--- |


#### 利用Cookie实现Session跟踪： <a id="&#x5229;&#x7528;Cookie&#x5B9E;&#x73B0;Session&#x8DDF;&#x8E2A;&#xFF1A;"></a>

如果WEB服务器处理某个访问请求时创建了新的HttpSession对象，它将把会话标识号作为一个Cookie项加入到响应消息中，通常情况下，浏览器在随后发出的访问请求中又将会话标识号以Cookie的形式回传给WEB服务器。

WEB服务器端程序依据回传的会话标识号就知道以前已经为该客户端创建了HttpSession对象，不必再为该客户端创建新的HttpSession对象，而是直接使用与该会话标识号匹配的HttpSession对象，通过这种方式就实现了对同一个客户端的会话状态的跟踪。

#### 利用URL重写实现Session跟踪： <a id="&#x5229;&#x7528;URL&#x91CD;&#x5199;&#x5B9E;&#x73B0;Session&#x8DDF;&#x8E2A;&#xFF1A;"></a>

Servlet规范中引入了一种补充的会话管理机制，它允许不支持Cookie的浏览器也可以与WEB服务器保持连续的会话。这种补充机制要求在响应消息的实体内容中必须包含下一次请求的超链接，并将会话标识号作为超链接的URL地址的一个特殊参数。

将会话标识号以参数形式附加在超链接的URL地址后面的技术称为URL重写。如果在浏览器不支持Cookie或者关闭了Cookie功能的情况下，WEB服务器还要能够与浏览器实现有状态的会话，就必须对所有可能被客户端访问的请求路径（包括超链接、form表单的action属性设置和重定向的URL）进行URL重写。

HttpServletResponse接口中定义了两个用于完成URL重写方法：

* encodeURL方法
* encodeRedirectURL方法

### Cookie和Session的不同： <a id="Cookie&#x548C;Session&#x7684;&#x4E0D;&#x540C;&#xFF1A;"></a>

session和cookies同样都是针对单独用户的变量（或者说是对象好像更合适点），不同的用户在访问网站的时候 都会拥有各自的session或者cookies，不同用户之间互不干扰。

他们的不同点是：

1. 存储位置不同

   session在服务器端产生，比较安全，但是如果session较多则会影响性能

   cookies在客户端产生，安全性稍弱

2. 生命周期不同

   session生命周期 在指定的时间（如20分钟）到了之后会结束，不到指定的时间，也会随着浏览器进程的结束而结束。

   cookies默认情况下也随着浏览器进程结束而结束，但如果手动指定时间，则不受浏览器进程结束的影响。

cookie 和session 的区别：

1. cookie数据存放在客户的浏览器上，session数据放在服务器上。
2. cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗 考虑到安全应当使用session
3. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能

   考虑到减轻服务器性能方面，应当使用COOKIE

4. 单个cookie在客户端的限制是3K，就是说一个站点在客户端存放的COOKIE不能3K。
