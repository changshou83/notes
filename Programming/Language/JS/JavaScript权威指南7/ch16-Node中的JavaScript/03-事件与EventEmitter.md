
- 在`API`是围绕对象而非函数设计的，或者回调需要多次被调用，或者需要多种类型的回调时，`API`就会是基于事件的
- 在`Node`中，发送事件的对象都是`EventEmitter`或其子类的实例

## 代码示例

```js
const net = require('net')
const server = new net.Server();

server.on('connection', socket => {
	socket.end('Hello World', 'utf-8')
})

// emit 触发
```
