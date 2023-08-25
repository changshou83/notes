## 期约

```js
// 串行Promise
function promiseSequence(inputs, promiseMaker) {
	inputs = [...inputs]
	
	function handleNextInput(outputs) {
		if(inputs.length === 0) {
			return outputs;
		} else {
			const nextInput = inputs.shift();
			return promiseMaker(nextInput)
				.then(output => outputs.concat(output))
				.then(handleNextInput);
		}
	}
	
	return Promise.resolve([]).then(handleNextInput);
}

promiseSequence(urls, (url) => fetch(url).then(r => r.text()))
	.then(bodies => `bodies: ${bodies}`)
	.catch(console.error);
```

## 异步迭代

- 由于期约只适合单次异步事件，所以新增了异步迭代解决重复性异步事件
- 异步迭代器允许我们表示异步事件流或数据流

```js
const promises = urls.map(url => fetch(url))
for await (const promise of promises) {
	handle(promise)
}
```

### 实现异步迭代器

```js
class AsyncQueue {
	constructor() {
		this.values = [];
		this.resolvers = [];
		this.closed = false;
	}
	enqueue(value) {
		if(this.closed) throw new Error('AsyncQueue closed');
		
		if(this.resolvers.length > 0) {
			this.resolvers.shift()(value)
		} else {
			this.values.push(value)
		}
	}
	dequeue() {
		if(this.values.length > 0) {
			return Promise.resolve(this.values.shift())
		} else if(this.closed) {
			return Promise.reject(AsyncQueue.EOS)
		} else {
			return new Promise(resolve => {
				this.resolvers.push(resolve)
			})
		}
	}
	// 关闭队列
	close() {
		while(this.resolvers.length > 0) {
			// 以 EOS 标记解决所有剩余期约
			this.resolvers.shift()(AsyncQueue.EOS);
		}
		this.closed = true;
	}
	// 成为异步可迭代对象
	[Symbol.asyncIterator]() { return this; }
	// 成为异步迭代器
	next() {
		return this.dequeue().then(value => {
			return value === AsyncQueue.EOS
				? { value: undefined, done: true }
				: { value: value, done: false };
		})
	}
}

AsyncQueue.EOS = Symbol('end-of-stream');

// 使用
function eventStream(e, type) {
	const q = new AsyncQueue()
	e.addEventListener(type, e => q.enqueue(e))
	return q
}
async function handleKeys() {
	// 取得一个 keypress 事件流，对每个事件都执行一次循环 
	for await(const evt of eventStream(document, 'keypress')) {
		console.log(event.key);
	}
}
```
