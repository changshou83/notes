## 脚本运行时机

- async
	- 解析HTML的同时同步解析js脚本，解析完立即执行
	- 谁先下载完谁先执行
- defer
	- 解析HTML的同时同步解析js脚本，解析完不立即执行，而是等到文档完全加载完(解析完成后，`DOMContentLoaded`事件触发前)才执行
	- 按照标签顺序

## 时间线

1. 浏览器创建DOM对象，并开始解析HTML，此时，`document.readState == 'loading'`
2. `HTML`解析器碰见一个纯`script`标签，将该标签加入到文档中，然后立即执行其代码
3. `HTML`解析器碰见带有`async`属性的`script`标签，在解析的同时开始下载其代码(递归下载其依赖模块)，待下载后立即执行
4. 文档解析完成后，`document.readState == 'interactive'`
5. 任何有`defer`属性的脚本按照他们在文档中出现的顺序开始执行(之前已同步下载)，延迟脚本可以访问完整的文档
6. `Document.DOMContentLoaded`事件触发，注意，此时仍然可能存在尚未执行的`async`脚本
7. 当所有外部资源都加载完成，所有`async`脚本也都执行完成后。`loaded`事件被触发
8. 然后，作为对用户输入事件，网络事件，定时器超时等的响应，浏览器开始异步调用事件处理程序

## Web安全模型

- 同源策略
	- 文档的源就是文档URL的协议、主机和端口
	- 浏览器通常把每个`file: URL`看成一个独立的源，这意味着如果你写的程序会显示同一台服务器上的多个文档，则可能无法使用`file: URL`在本地测试它，而必须在开发期间运行一个**静态Web服务器**
	- 缓解同源策略
		1. 通过`document.domain`修改源，可以用在多子域的大型网站上
		2. 跨域资源共享(CORS)：通过请求头`Origin`和响应头`Access-Control-Allow-Origin`来告知浏览器如何放松同源限制
- XSS(跨站点脚本)

## 补充
### `DocumentFragment`节点

- 节点类型有很多，但是没有能表示文档片段的类型
- 可以通过`createDocumentFragment()`创建`DocumentFragment`节点
- 向文档中插入`DocumentFragment`节点时，其本身并不会被插入，插入的是子节点
