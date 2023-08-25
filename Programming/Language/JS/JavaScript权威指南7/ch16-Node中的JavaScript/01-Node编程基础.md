
- Node的典型特点是由其默认异步的API赋能的单线程基于事件的并发能力
- 命令行参数：第一个始终是Node可执行文件的路径，第二个是Node执行的JavaScript代码文件的路径，之后就是其余的命令行参数
- 生命周期
	- Node天生异步，因此有很多Node程序并非执行完最后一行代码就退出，因为还要等待事件的触发
	- 可以通过调用`process.exit()`方法强制退出
	- 如果代码抛出异常
		- 默认其必须被局部处理，否则会导致程序崩溃
		- 也可以注册一个全局错误处理函数(用于清理之前分配的资源)：`process.setUncaughtExceptionCaptureCallback(e => {})`
		- 处理未`catch`的期约错误：`process.on('unhandleRejection', (reason, promise) => {})`
- Node适合I/O密集型应用，不适合CPU密集型应用

## 相关代码示例

```js
// 模块系统
const { readFile: rf, readFileSync: rfs } = require('fs')
import { readFile as prf } from 'fs/promises'

// 回调版
function readConf(path, cb) {
	rf(path, 'uft-8', (err, text) => {
		if(err) {
			console.error(err)
			cb(null)
			return;
		}
		let data = null
		try {
			data = JSON.parse(text)
		} catch(e) {
			console.error(e)
		}
		cb(data)
	})
}

// promise版
function readConf(path) {
	return prf(path, 'uft-8').then(text => {
		return JSON.parse(text)
	})
}

// async/await版
async function readConf(path) {
	const text = await prf(path, 'uft-8')
	return JSON.parse(text)
}

// 同步版
function readConfSync(path) {
	let text = rfs(path, 'uft-8')
	return JSON.parse(text)
}
```

