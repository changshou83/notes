- 一类数据结构，常用于从文件或网络读取数据
- 是字节序列而非字符序列
- 在Node支持定型数组后，变为`Uint8Array`的子类，与超类的区别在于，Buffer用来操作字符串
- 只要有字符串和字符编码，就可以将该字符串中的字符编码为字符序列，有正确的字符序列和字符编码，就可以正确解码出字符串
- Node支持的编码
	- uft-8
	- uft16le
	- latin1
	- ascii
	- hex
	- base64

## 代码示例

```js
const b = Buffer.from([0x41, 0x42, 0x43])
b.toString()       // "ABC"
b.toSTring('hex')  // 414243

const computer = Buffer.from('IBM3111', 'ascii')
for(let i = 0; i < computed.length; i++) {
	computed[i]--;
}

const zeros = Buffer.alloc(1024)
const ones = Buffer.alloc(128, 1)
```
