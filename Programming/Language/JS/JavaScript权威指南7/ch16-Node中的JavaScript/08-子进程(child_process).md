
- `execSync()`和`exec()`：执行一个命令
- `execFileSync()`和`execFile()`：执行一个可执行文件
- `spawn()`
	- 可以创建子进程
	- 允许子进程流式访问子进程的输出，也允许向子进程写入数据，也支持与子进程交互
	- 类似于基于事件的`execFile()`
- `fork()`
	- 可以创建子进程
	- 用于在一个Node子进程中运行一段JavaScript代码
	- 可以在父子进程间建立简单的通信方式，父子进程通过send发送数据，监听`message`事件接收数据

### 代码示例

```js
const child_process = require('child_process');

// execSync
const listing = child_process.execSync('ls -l web/*.html', { encoding: 'uft8' })

// execFileSync
const listing_2 = child_process.execFileSync('ls', ['-l', 'web/*.html'], { encoding: 'uft8' })

// fork
const child = child_process.fork(`${__dirname}/child.js`);
child.send({ x: 4, y: 3 });
child.on('message', msg => {
	child.disconnect();
})

// child.js
process.on('message', msg => {
	console.log(msg)
	process.send(null)
})
```

