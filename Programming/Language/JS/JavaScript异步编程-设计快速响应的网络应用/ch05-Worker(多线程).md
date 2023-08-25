
- worker不是用来实现传统的多线程的，只是为了把计算密集型任务分发出去

```js
// 创建工作线程
const worker = new Worker('./gcd.js');
// 与工作线程通信
worker.postMessage([273,159]);
worker.addEventListener('message', (ret) => {
	console.log(ret);
});
// 工作线程执行完所有任务，自动销毁
```
