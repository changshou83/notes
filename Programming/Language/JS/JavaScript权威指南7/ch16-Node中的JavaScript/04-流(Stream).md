
- 用途：以高效的方式处理读/写文件、网络通信、或任何类型的端到端的信息交换
- 基本流：可读流/可写流/双工流/转换流

## 流

### 使用流的API

| 名称                     | 功能                                             |
| ------------------------ | ------------------------------------------------ |
| `process.stdin`          | 返回连接到 stdin 的流                            |
| `process.stdout`         | 返回连接到 stdout 的流                           |
| `process.stderr`         | 返回连接到 stderr 的流                           |
| `fs.createReadStream()`  | 创建文件的可读流                                 |
| `fs.createWriteStream()` | 创建到文件的可写流                               |
| `net.connect()`          | 启动基于流的连接                                 |
| `http.request()`         | 返回 http.ClientRequest 类的实例，该实例是可写流 |
| `zlib.createGzip()`      | 使用 gzip（压缩算法）将数据压缩到流中            |
| `zlib.createGunzip()`    | 解压缩 gzip 流                                   |
| `zlib.createDeflate()`   | 使用 deflate（压缩算法）将数据压缩到流中         |
| `zlib.createInflate()`   | 解压缩 deflate 流                                |

### 代码示例

```js
const { readFile, createReadStream } = require('fs')
const http = require('http')

// 不使用流
function fn(req, res) {
	readFile(__dirname + '/data.txt', (err, data) => {
		res.end(data)
	})
}

// 使用流
function fn(req, res) {
	createReadStream(__dirname + '/data.txt').pipe(res)
}

const server = http.createServer(fn).listen(3000)
```

## 管道

- `pipe()`：获取来源流，并将其通过管道传输到目标流

### 与转换流一起使用

```js
const fs = require('fs')
const zlib = require('zlib')

function gzip(filename, cb) {
	const source = fs.createReadStream(filename)
	const dest = fs.createWriteStream(filename + '.gz');
	const gzipper = zlib.createGzip()

	source
		.on('error', cb)
		.pipe(gzipper)
		.on('error', cb)
		.on('finish', cb)
}
```

## 自定义流

```js
const stream = require('stream')

class GrepStream extends stream.Transform {
	constructor(pattern) {
		super({ decodeStrings: false }); // 不把字符串转换回缓冲区
		this.pattern = pattern
		this.incompleteLine = ''
	}

	// 在转换字符串时被调用
	_transform(chunk, encoding, cb) {
		if(typeof chunk !== 'string') {
			cb(new Error('Excepted a string but got a buffer'))
			return;
		}
		
		let lines = (this.incompleteLine + chunk).split('\n')
		this.incompleteLine = lines.pop();
		
		let output = lines.filter(l => this.pattern.test(l)).join('\n')
		if(output) output += '\n'
		cb(null, output)
	}

	// 在流关闭前调用
	_flush(cb) {
		if(this.pattern.test(this.incompleteLine)) {
			cb(null, this.incompleteLine + '\n')
		}
	}
}

const pattern = new RegExp(process.argv[2])
process.stdin
	.setEncoding('uft8')
	.pipe(new GrepStream(pattern))
	.pipe(process.stdout)
	.on('error', () => process.exit())
```

## 可写流与背压

- 背压
	- 一种消息，表示你向流中写入数据的速度超过了它的处理能力
	- write()方法返回false值是一种背压（backpressure）的表现
	- 如果不处理背压，可能会被Dos攻击弄崩溃
- 如何处理背压
	- 调用`pipe()`，Node会自动处理背压
	- 在`write()`返回`false`时，暂停写入；在`drain`事件出发之后启动写入

## 异步迭代

```js
// 使用 for/await 重写 grepstream
async function grep(source, dest, pattern, encoding="uft8") {
	source.setEncoding(encoding);
	dest.on('error', err => process.exit())
	
	let incompleteLine = '';
	for await(let chunk of source) {
		let lines = (incompleteLine+chunk).split('\n');
		incompleteLine = lines.pop();
		for(let l of lines) {
			if(pattern.test(l))
				dest.write(l+'\n', encoding);
		}
	}
	
	if(pattern.test(incompleteLine))
		dest.write(incompleteLine+'\n', encoding);
}

const pattern = new RegExp(process.argv[2])
grep(process.stdin, process.stdout, pattern)
	.catch(err => {
		console.error(err);
		process.exit();
	})
```

## 通过事件读取流

- Node可读流有两种模式，如果不能使用管道或异步迭代，就需要一种模式来处理流
	- 流动模式
		- 当可读数据到达时，会**立即**以“data”事件的形式发送
		- `read()`：在流动模式下无须调用read()方法，只需要处理“data”事件。新创建的流并非一开始就处于流动模式。注册“data”事件处理程序会把流切换为流动模式
		- `pause()`：如果读文件的时候同时向一个可写流写入，那么在可写流的缓冲区满了(`output.write() === null`)之后可以调用`parse`方法暂停`data`事件
		- `resume()`：待从可写流收到'耗尽(drain)'事件时，可以调用`resume()`方法，再次启动`data`事件
		- 在到达流末尾时发出一个`end`事件
	- 暂停模式
		- 流开始时所处的模式
		- 需要显式调用其`read()`方法从流中拉取数据
		- 需要监听`readable`事件，该事件被触发表明流中有可读数据，需要不断调用read方法直至返回`null`，这样才能触发新的`readable`事件
		- 不好用，不如流动模式（或管道）简单
