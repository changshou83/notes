
- 支持多线程编程，但是又不一样，因为JavaScript线程默认不共享内存，因此不存在资源死锁问题
- JavaScript线程通过事件通信
- 使用工作线程的理由
	- 应用所需计算量大于一个CPU，工作线程可以把任务分配给多核心
	- 需要主线程快速响应，可以将大计算量的任务分配给工作线程去做，主线程用来响应请求
	- 需要将遗留代码中的同步操作转为异步操作，可以用工作线程来执行遗留代码来避免同步阻塞
- 总之，如果不是CPU密集型应用，没有响应性问题，那么就不考虑工作线程
- PS：有这些问题应用也许也不会用Node来做，毕竟还有其它语言可以选，Node的强项就是I/O密集型任务处理快。

## 简单Worker示例

- `process.env`
	- 默认情况下，`process.env`在工作线程中是父线程中`process.env`的一个副本
	- 父线程可以通过设置`Worker()`构造函数第二个参数的`env`属性指定一组自定义的环境变量
- `process.stdin`
	- 默认情况下，`process.stdin`流在工作线程中永远不会有任何可读数据
	- 可以通过给`Worker()`构造函数的第二个参数传`stdin:true`来改变这个默认设置
- `process.stdout`和`process.stderr`
	- 默认情况下，process.stdout和process.stderr在工作线程中都会简单地被引流到父线程中对应的流
	- 可以通过给`Worker()`构造函数的第二个参数传`stdout:true`来改变这个默认设置
- `process.exit()`：只有当前线程会退出，而不是整个进程都退出
- 工作线程不能改变它们所属进程的共享状态
- 操作系统信号只发送到主线程，工作线程不能接收和处理它们

```js
const threads = require('worker_threads');

// isMainThread 属性判断是否为主线程
// 用来实现主线程代码和工作线程代码共存于同一个文件
if(threads.isMainThread) {
	module.exports = function reticluateSpline(splines) {
		return new Promise((resolve, reject) => {
			// 创建工作线程
			const reticluator = new threads.Worker(__filename);
			// 将数据传送给工作线程,通过threads.parentPort.on('message')接受
			reticulator.postMessage(splines);

			// 接收到工作线程的数据时解决 promise，否则拒绝
			reticulator.on('message', resolve);
			reticulator.on('error', reject);
		})
	}
} else {
	// 此处在工作线程中
	// 可以通过threads.parentPort对象与父进程交互
	// 给来自父线程的消息注册一个一次性事件，会在完成任务后自动退出
	threads.parentPort.once('message', splines => {
		for(let spline of splines) {
			spline.reticulate
				? spline.reticulate()
				: (spline.reticulated = true);
		}
		// 传送数据到父线程，通过worker.on('message', ...)接受
		threads.parentPort.postMessage(splines)
	})
}
```

## 通信信道

- 在新建工作线程的时候，会默认创建一个父线程与工作线程之间的通信信道，用于父子线程通信
- 使用`MessageChannel()`构造函数可以创建一个信道，`MessageChannel`对象上有两个属性`port1`，`port2`是两个不同的`MessagePort`对象

## 在线程间转移定型数组

```js
const { Worker, MessageChannel } = require('node:work_threads');

const worker = new Worker();
const channel = new MessageChannel();

// 转移 port1 到工作线程
worker.postMessage({
	command: 'changeChannel',
	data: channel.port1
}, [channel.port1]);
// 转移 定型数组 到工作线程
const pixels = new Uint8Array(1024*1024);
worker.postMessage(pixels, [pixels.buffer])

channel.port2.postMessage('Can you hear me now?');
channel.port2.on('message', handleMessagesFromWorker);
```
