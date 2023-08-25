
- 状态码告知从服务器端返回的请求结果
- 状态码只能显示HTTP请求的状态，可能与实际情况不符合

## 类别

### 1XX

- 信息型状态码，当前的请求正在被处理
- 101 Continue：一切正常
- 102 Switching Potocols：协议升级(WebSocket会用到)

### 2XX

- 成功状态码，当前的请求正常处理完毕
- 200 OK：请求成功
- 204 No Content
	- 表示该请求已经成功了，但是客户端客户不需要离开当前页面
	- 使用惯例是，在 [`PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT) 请求中进行资源更新，但是不需要改变当前展示给用户的页面
- 206 Partial Content：范围请求

### 3XX

- 重定向状态码，需要完成一些附加操作完成请求
- 301 Moved Permanently：永久重定向，以后请使用新的URI访问
- 302 Found：临时重定向，此次请使用新的URI访问，GET方法
- 303 See Other：
	- 通常作为 [`PUT`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/PUT) 或 [`POST`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Methods/POST) 操作的返回结果
	- 重定向链接指向的不是新上传的资源，而是另外一个页面。
	- 获取资源请使用GET方法
- 304 Not Modified
	- 与重定向，与缓存有关；
	- 资源未改动，请使用缓存
- 307 Temporary Redirect：同302，不过不改变请求方法

### 4XX

- 客户端错误状态码，服务器无法处理请求
- 400 Bad Request：请求报文错误
- 401 Unauthorized：需进行身份验证才能访问
- 403 Forbidden：该资源禁止访问
- 404 Not Found：服务器无目标资源

### 5XX

- 服务端错误状态码，服务器处理请求出错
- 500 Internal Server Error：服务器报错
- 503 Service Unavailable：服务器繁忙
