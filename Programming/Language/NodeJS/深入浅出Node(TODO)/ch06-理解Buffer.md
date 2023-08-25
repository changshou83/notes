
- Node需要处理网络流和文件，还需要处理大量二进制数据，因此有了`Buffer`对象

## Buffer结构

- 像是数组但是用于操作字节(PS:`TypedArray`?)
- 模块结构
	- 性能相关由`C++`实现，非性能相关用`JS`实现
	- Buffer是堆外内存，内存由C++申请，在JS里分配，因此不受V8的内存限制
	- Buffer在全局对象上，无需引入
- 对象结构
	- 类似于数组，元素为16进制的两位数(0-255)，示例：`new Buffer(string[, encoding])`或者`new Buffer(size)`
	- 由`length`属性，可以通过下标访问和赋值(`x>255 ? x-256*n : x<0 ? x+256*n : Number.isInteger(x) ? x : Math.floor(x)`)
- 内存分配使用`slab`机制
	- 一种动态内存管理机制
	- slab是一块申请好的固定大小的内存区域,它有三种状态：`full`,`partial`,`empty`
	- 以`Buffer.poolSize`(8192,8KB,一个`slab`的大小)区分大对象和小对象
		- 小对象，预先申请和事后分配
		- 大对象，直接使用C++提供的内存，不进行分配

## Buffer转换

- 可以与字符串相互转换，支持的编码：`ascii`，`utf-8`，`utf16`，`base64`，`binary`，`hex`

```js
// 字符串转buffer，buf.write(string[, offset[, length[, encoding]]])
const buf1 = new Buffer('string', 'utf8');
const buf2 = new Buffer(100)
buf2.write('string', 0, 6, 'utf8');

// buffer转字符串,buf.toString([encoding[, start[, end]]])
const str = buf1.toString()

// 是否为支持类型
Buffer.isEncoding('gbk') // => false
```

## Buffer拼接

第一种方法是直接使用`+=`，即调用`toString`方法会出现乱码问题，因为`toString`的默认编码是`utf8`，因此在转为字符串的时候会截断字节导致解码失败。
第二种方法是调用可读流的`setEncoding`方法设置编码，可以将`Buffer`编码为字符串并传给回调函数，但是只支持有限的编码。
因此正确的方法应该是使用数组接受完整的`Buffer`，然后使用`Buffer.concat`方法合并，之后再用`iconv`进行解码。

```js
const iconv = require('iconv');
const fs = require('fs');

const stream = fs.createReadStream('test.js');
let size = 0;
const chunks = [];

stream.on('data', (chunk) => {
	chunks.push(chunk);
	size += chunk.length;
})
stream.on('end', () => {
	const buf = Buffer.concat(chunks, size);
	const str = iconv.decode(buf, 'utf8');
})
```
