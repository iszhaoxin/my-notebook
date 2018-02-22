## python-urllib2

### 1. Functions in Urllib2

-   urllib2.urlopen(url[, data[, timeout[, cafile[, capath[, cadefault[, context]]]]])

    -   这里的url可以是一个string,也可以是一个request对象

    -   data是向服务器发送的特殊数据,目前只有HTTP协议支持这项功能.在data非None的时候,HTTP将是以POST代替GET发送请求.这里的data中的数据应该是经过<urllib.urlencode()>编码,将一个二元的元组数据格式转换为百分比编码,发送到HTTP.

    -   timeout是指等待成功连接的默认时间,若是超过这个时间将会被挂起.(对于HTTP,HTTPS,FTP有效).设置timeout的方法是:

        ```python
        import socket
        socket.setdefaulttimeout(timeout)
        ```

        也就是说没必要对urllib2下的对象进行操作.

    -   cafile,capath指定了受信任的CA证书,cafile指向了一捆证书,capath指向哈希的证书文件.cadefault忽略

    -   这个函返回一个类文件的对象,除了文件有的特性之外,还有三个额外操作函数

        -   geturl():就不说了,都懂
        -   info() :return the meta-information of the page, such as headers
        -   getcode():状态符,一般为200

    -   当发生错误是会抛向URLError,这时返回的对象为None

-    urllib2.install_opener(opener) and   urllib2.build_opener([handler, ...])

    -   build_opener,就是构建一个个性的opener,opener对象有一个open方法，它可以以一种和urlopen函数同样的方式直接调用来获取url. 

    -   install_opener是将默认的opener改为设定的,除非是为了方便,没有必要调用install_opener。

    -   opener是什么?:

        -   其实opener我们一直再用,就是上面的urlopen(),urlopen函数将我们的请求打包成一个数据包然后发送给服务器,这就是opener的功能.但是仅靠opener是无法实现的,还要靠handler,opener+handler才可以实现上述功能.

    -   handler是什么?

        -   handler知道怎么以特定的url协议打开url.
        -   OpenerDirector操作类是一个管理很多处理类（Handler）的类。而所有这些 Handler 类都对应处理相应的协议，或者特殊功能。

    -   opener与handler的关系是:

        -   Openers使用处理器handlers，所有的“繁重”工作由handlers处理。

        -   每个handlers知道如何通过特定协议打开URLs，或者如何处理URL打开时的各个方面。 

        -   先构建一个handler,在将handler作为参数传到 urllib2.build_opener中构建opener

        -   代码示例如下:

            ```python
            http_handler = urllib2.HTTPSHandler()
            opener = urllib2.build_opener(http_handler)
            request = urllib2.Request("http://www.baidu.com/")
            response = opener.open(request)
            ```

            当然从这个例子中是看不出有什么用的,具体的应用见下一节.

-   如何利用自己定义的opener以及handler?

    其实说,自己定义也只是改一些参数,选择一些特定的协议解释而已.下面介绍三个例子:

    -   增加调试信息(会把收包和发包的报头在屏幕上自动打印出来) :

        ```python
        https_handler = urllib2.HTTPSHandler(debuglevel=1)
        opener = urllib2.build_opener(https_handler)
        request = urllib2.Request("http://www.baidu.com/")
        response = opener.open(request)
        print response.read()
        ```

    -   ProxyHandler处理器（代理设置）(极其重要!!!!!!)

        ```python
        httpproxy_handler = urllib2.ProxyHandler({"http" : "124.88.67.81:80"})
        nullproxy_handler = urllib2.ProxyHandler({})
        proxySwitch = True #定义一个代理开关
        if proxySwitch:  
            opener = urllib2.build_opener(httpproxy_handler)
        else:
            opener = urllib2.build_opener(nullproxy_handler)
        request = urllib2.Request("http://www.baidu.com/")
        ```

        这里顺便附一个[代理服务器网址](http://31f.cn/),喜欢玩的可以爬一些具有商业价值的网站了.

        这里在记一波代理的概念:

        ​	其功能就是代理网络用户去取得网络信息。形象的说：它是网络信息的中转站。在一般情况下，我们使用网络浏览器直接去连接其他Internet站点取得网络信息时，须送出Request信号来得到回答，然后对方再把信息以bit方式传送回来。代理服务器是介于浏览器和Web服务器之间的一台服务器，有了它之后，浏览器不是直接到Web服务器去取回网页而是向代理服务器发出请求，Request信号会先送到代理服务器，由代理服务器来取回浏览器所需要的信息并传送给你的浏览器。

    -   Basic Authentication 基本验证

        一个通过设置handler解决基础验证(密码账号)的方法,代码如下,附带[引用链接](www.jb51.net/article/57141.htm):

        ```python
        # 创建一个密码管理者 
        password_mgr = urllib2.HTTPPasswordMgrWithDefaultRealm() 
        # 添加用户名和密码 
        top_level_url = "http://example.com/foo/" 
        # 如果知道 realm, 我们可以使用他代替 ``None``. 
        # password_mgr.add_password(None, top_level_url, username, password) 
        password_mgr.add_password(None, top_level_url,'why', '1223') 
        # 创建了一个新的handler 
        handler = urllib2.HTTPBasicAuthHandler(password_mgr) 
        # 创建 "opener" (OpenerDirector 实例) 
        opener = urllib2.build_opener(handler) 
        a_url = 'http://www.baidu.com/' 
        # 使用 opener 获取一个URL 
        opener.open(a_url) 
        # 安装 opener. 
        # 现在所有调用 urllib2.urlopen 将用我们的 opener. 
        urllib2.install_opener(opener)
        ```

        ​




### 2. exception in urllib2

-   URLError

    The urllib.error module defines the exception classes for exceptions raised by urllib.request. The base exception class is URLError.

    首先解释下URLError可能产生的原因：

    -   网络无连接，即本机无法上网
    -   连接不到特定的服务器
    -   服务器不存在

    在实际编程时,需要用try-except来捕捉:

    ```python
    try:  
    	response=urllib2.urlopen(req) 
    except urllib2.URLError,e:
        print e.reason
    ```

-   HTTPError

    -   含有两个变量,一个函数可以调用
        -   reason,code,geturl()



### 3. classes in urllib2

#### 1. urllib2.Request

 class urllib2.Request(url[, data\]\[, headers\]\[, origin_req_host\]\[, unverifiable\])

-   描述:
    -   This class is an abstraction of a URL request.
    -   url and data is same as urlopen
    -   headers:是用来修改request的header信息的,具体request有哪些header详见另外的文章.这里的话,举一个例子,就是欺骗服务器,发送信息来源,比如说将本身的来自urllib的请求信息改为来自Mozilla的.
    -   剩下的两个据说不重要就不管了
-   包含函数:
    -   Request.add_data(data):向data中添加数据,多为post信息.
    -   Request.get_method():获取该请求的方法,是POST还是GET
    -   Request.has_data():看看实例中是否有data
    -   Request.get_data():返回实例中的data
    -   Request.add_header(key, val):刚才说过了
    -   Request.add_unredirected_header(key, header):这里增加的数据不会传送到重定向的网页里
    -   Request.has_header(header):so.so.
    -   Request.get_full_url().ma.ma
    -   Request.get_type():获取协议类型.
    -   Request.get_host():获取主机地址
    -   Request.get_selector():获取url中除了主机地址之外其他的部分
    -   Request.header_items():返回header的列表
    -   Request.get_header(header_name, default=None):返回指定属性的header
    -   Request.set_proxy(host, type):准备连接到代理服务器的请求。主机和类型将取代那些实例,实例的选择器将在构造函数中给出的原始URL。
    -   Request.is_unverifiable():Return whether the request is unverifiable,返回请求是否核实

#### 2. OpenerDirector Objects

-    The OpenerDirector class opens URLs via BaseHandlers chained together. It manages the chaining of handlers, and recovery from errors.

     OpenerDirector通过与它连接在一起的BaseHandlers打开URL,它管理着与handlers的链接,并且可以从错误中恢复.OpenerDirector操作类是一个管理很多处理类（Handler）的类


-   包含函数
    -   OpenerDirector.add_handler(*handler*):可以理解为实现了将某个hander绑定到opener的功能,这里的handler应该是一个实例.
    -   OpenerDirector.open(url[, data\]\[, timeout\]):打开url
    -   OpenerDirector.error(proto[, arg[, ...]]):处理给定协议错误,这个将会调用给定的协议的注册错误处理器.
-   OpenerDirector objects open URLs in three stages:OpenerDirector 分三个阶段打开url:
    -   每一个处理器(hander)会有一个名为`protocol_request`的预处理请求
    -   `protocol_open`去处理请求
    -   `protocol_response`去后处理请求

#### 3. BaseHandler

​	This is the base class for all registered handlers — and handles only the simple mechanics of registration.

#### 4. HTTPDefaultErrorHandler

​    A class which defines a default handler for HTTP error responses; all responses are turned into HTTPError exceptions.

####　5. HTTPRedirectHandler

A class to handle redirections.

#### 6. HTTPCookieProcessor

A class to handle HTTP Cookies.

#### 7. ProxyHandler

这个的用法在上面已经介绍过了,只有一个函数,就是

 ProxyHandler.protocol_open(request)

具体工作过程如下:

The ProxyHandler will have a method `protocol_open` for every protocol which has a proxy in the proxies dictionary given in the constructor. The method will modify requests to go through the proxy, by calling request.set_proxy(), and call the next handler in the chain to actually execute the protocol.

即,ProxyHandler会调用`protocol_open`去给所用的协议分配其代理,代码样式如下:

```python
httpproxy_handler = urllib2.ProxyHandler({"http" : "124.88.67.81:80"})
```

这个函数会通过`request.set_proxy()`去把请求发向协议中地址,然后在调用**链表**中的下一个处理器去执行协议.

>   注意:上面的**call the next handler in the chain to actually execute the protocol.**说明,一个请求的发送是有其处理顺序的,也许,也许,本身从最初的函数中的参数以及url这种由用户构建的简单"请求",要经过很多个连在一起的handler的处理才可以变成可以在网络中流转的数据包,这样我们这里的handler的设置应该只是改其中的每一个小环节.

#### 8. HTTPPasswordMgr Objects

-   HTTPPasswordMgr.add_password(realm, uri, user, passwd)
    -   *uri* can be either a single URI, or a sequence of URIs. *realm*, *user* and
        *passwd* must be strings. This causes `(user, passwd)` to be used as
        authentication tokens when authentication for *realm* and a super-URI of any of
        the given URIs is given.
-    HTTPPasswordMgr.find_user_password(realm, authuri)
    -   ​    Get user/password for given realm and URI, if any. This method will return (None, None) if there is no matching user/password.For HTTPPasswordMgrWithDefaultRealm objects, the realm None will be searched if the given realm has no matching user/password.

#### 9.其他

还有一些其他的不重要的我就不记了,累

剩下的需要时再看,[链接](https://docs.python.org/2/library/urllib2.html#proxyhandler-objects)