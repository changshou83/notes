- C(lient)/S(erver)架构
- 默认端口80
- 无状态协议：HTTP不记录应用状态，这可以节省服务器的CPU和内存资源
- 明文传输
- HTTP方法
	- GET：获取资源
	- POST：传输实体主体
	- PUT：传输文件，不验证，不安全
	- DELETE：删除文件，不验证，不安全
	- HEAD：获取报文首部，用于确认URI的有效性及资源更新的日期时间等
	- OPTIONS：询问支持的方法
- 持久连接
	- 解决的问题：每次传输都需要重新建立TCP链接
	- 方案：只要有一方不明确终止，TCP链接就不断开。
	- 好处：节省了重复建立TCP链接的资源。
- 管线化
	- 应用前：以前必须上一个请求响应完才能发送下一个请求
	- 应用后：不必等待上一个响应完，即可立即发送下一个请求

## 报文格式

### 请求报文

```txt
Method URI Version
RequestHeader

Body
```

### 响应报文

```txt
Version StatusCode StatusReason
ResponseHeader

Body
```

## Cookie

- 用于
	- 会话状态管理：如用户登录状态、购物车或其它需要记录的信息
	- 个性化设置：如用户自定义设置、主题和其他设置
	- 浏览器行为跟踪：如跟踪分析用户行为等
- 创建Cookie
	- 客户端请求，服务端响应，并在响应头里设置`Set-Cookie`响应头
	- 生命周期
		- _会话期_ Cookie：在当前的会话结束之后删除
		- _持久性_ Cookie：过期时间（`Expires`）或有效期（`Max-Age`）过后被删除
- 限定访问
	- Secure：只应通过被 HTTPS 协议加密过的请求发送给服务端
	- HttpOnly：仅作用于服务器，可用于防止[XSS攻击](ch11-Web的攻击技术.md#^441d0e)
- 作用域
	- 允许 Cookie 应该发送给哪些 URL
	- Domain：指定了哪些主机可以接受 Cookie
	- Path：指定了一个 URL 路径，该 URL 路径必须存在于请求的 URL 中
	- SameSite：允许服务器指定是否/何时通过跨站点请求发送Cookie，可用于防止[CSRF攻击](ch11-Web的攻击技术.md#^14562c)
		- Strict：仅发送到它来源的站点
		- Lax(default)：只在用户导航到Cookie的源站点才发送
		- None：指定浏览器仅在安全上下文(HTTPS)中继续发送

## HTTP 缓存

- 定义：存储与请求关联的响应，并将存储的响应复用于后续请求
- 优点：响应速度快，服务器压力减小
- 类型
	- 私有缓存：浏览器缓存，用于存储客制化需求
	- 共享缓存：代理缓存，托管缓存(CDN,Service Worker)

### 基于age的缓存策略

- 缓存状态：`fresh`(有效)/`stale`(过期)
- 状态使用`age`(存活时间)和`max-age`(最大存活时间)区分,如果age大于`max-age`就过期，否则有效
- HTTP/1中,使用`expires`规定过期日期

### 缓存验证

- 对过期的缓存的状态进行更新
- 使用`If-Modified-Since`，`If-None-Match`条件请求完成
- `If-Modified-Since`
	- 服务器通过`Last-Modified`判断资源是否更新
	- 如果无需更新，返回304，浏览器更新缓存状态；否则返回200和新资源
	- 问题：时间格式复杂难以解析;分布式服务器的同步问题;
- `If-None-Match`
	- 客户端将`ETag`的值放入`If-None-Match`，询问资源是否被更改
	- 返回结果同上
	- `ETag` 响应头的值是服务器生成的任意值
- 在缓存验证时，`ETag`比`Last-Modified`优先。但是`Last-Modified`不止用于缓存验证
- 强制更新：`Cache-Control: no-cache`和`ETag`,`Last-Modified`

### 缓存破坏

- 对于内容会变化的资源，最佳实践是每次内容变化时都更改URL，这样可以使得响应被缓存更长时间，例如：添加一个hash值
- 主资源
	- 主资源不能被缓存破坏，例如：`.html`，`favicon.ico`
	- 但是可以使用cdn缓存主要资源，之后只要更新cdn缓存就行