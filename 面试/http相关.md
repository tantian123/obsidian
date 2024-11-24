
HTTP轮子
在浏览器的地址栏里输入一个URL，然后按下回车的时候到底发生了什么呢？那些奇奇怪怪的Header到底表示着什么呢？什么是代理？什么是反向代理 什么是网关呢？

=================
http请求和响应
请求行、请求头、请求体  

请求头主要存放对客户端想给服务端的附加信息
- Host: 目标服务器的网络地址
- Accept: 让服务端知道客户端所能接收的数据类型，如text/html
- Content-Type: body中的数据类型，如application/json; charset=UTF-8
- Accept-Language: 客户端的语言环境，如zh-cn

请求体是真正需要发给服务端的数据，
HTTP请求的请求体：
- form-data
- x-www-from-urlencoded
- raw
- binary

1.表单类型
2.表单，键值对类型的表单 例如key1=value1&key2=value2
3.任意格式的文本（看请求头的Content-Type） text、javascript、json、html、application/xml
服务器不会解析请求体，请求体的处理需要自己解析
4.通常用来上传文件
区分是否被当成文件的关键是Content-Disposition是否包含filename
为文件有不同的类型，所以还要使用Content-Type请求头指示文件的类型，如果不知道是什么类型取值可以为application/octet-stream表示该文件是个二进制文件

MIME类型
MIME类型通过`Content-Type`头字段来指定
作用：当客户端发送数据给服务器时，`Content-Type`头字段告诉服务器请求体的数据格式
当服务器发送响应给客户端时，`Content-Type`头字段告诉客户端如何处理数据（如渲染网页、解析JSON等）
（它确保了客户端和服务器能够正确地识别和解析传输的数据）


===============
[HTTP状态码详解 (oschina.net)](https://tool.oschina.net/commons?type=5)

http状态码 是标准的 用来反映网络状态而不是业务状态
2xx请求成功
3xx资源转移
4xx代表你的**请求**存在某种问题
5xx代表**服务器**本身有问题

例如 200代表成功 301/302表示重定向
401需要认证，403代表没有权限，404代表资源不存在，405代表请求方法不支持，406代表请求的参数有误
502错误网关，504服务不可用，504超时

一般做法是 success(200) / error(非200)+body里面自定义业务码


===========================
http1.1
HTTP 1.1 支持了 keep-alive，只要请求或响应头带上 Connection: keep-alive，就可以告诉对方先不要断开链接，我之后还要用这个链接发消息
这样就可以**用同一个 TCP 链接进行多次 HTTP 请求响应**了
但这样虽然减少了链接的建立，在性能上却有问题，下次请求得等上一个请求返回响应才能发出，
这个问题有个名字，叫做**队头阻塞**

还有个问题， header 部分太大了


HTTP 2.0 出现了，做了很多性能优化
对于队头阻塞的问题，每个请求、响应都加上一个 ID，然后每个响应和通过 ID 来找到它对应的请求
对于header太大，文本传输太占空间，换成二进制的