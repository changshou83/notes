
- 普遍应用于需要将二进制数据编码为普通文本进行传输的场景
- atob：对base64编码的字符串进行解码
- btoa：对二进制数据进行base64编码

```js
let encodedData = window.btoa("Hello, world"); // 编码
let decodedData = window.atob(encodedData);    // 解码
```

## Unicode 字符串

- JavaScript字符串使用utf16编码，而base64仅将二进制数据做输入，因此需要将JavaScript字符串处理为ascii字符串

```js
// 将utf16字符串处理为ascii字符串
function toBinary(string) {
	// 将字符串处理为定型数组
	const codeUnits = new Uint16Array(string.length);
	for(let i = 0; i < codeUnits.length; i++) {
		codeUnits[i] = string.charCodeAt(i);
	}
	// 将定型数组处理为ascii字符串
	const charCodes = new Uint8Array(codeUnits.buffer);
	let result = '';
	// 注意是byteLength
	for(let i = 0; i < charCodes.byteLength; i++) {
		result += String.fromCharCode(charCodes[i]);
	}
	return result;
}
// 将ascii字符串编码为utf16字符串
function fromBinary(binary) {
	// 将ascii字符串处理为定型数组
	const bytes = new Uint8Array(binary.length);
	for(let i = 0; i < bytes.length; i++) {
		bytes[i] = binary.charCodeAt(i);
	}
	// 将定型数组处理为utf16字符串
	const charCodes = new Uint16Array(bytes.buffer);
	let result = '';
	for(let i = 0; i < charCodes.length; i++) {
		result += String.fromCharCode(charCodes[i]);
	}
	return result;
}

// 使用
const myStr = "☸☹☺☻☼☾☿";

const converted = toBinary(myStr);    // 转变为ascii字符串
const encoded = btoa(converted);      // 编码为base64字符串

const decoded = atob(encoded);        // 解码为ascii字符串
const original = fromBinary(decoded); // 转变为utf16字符串
```
