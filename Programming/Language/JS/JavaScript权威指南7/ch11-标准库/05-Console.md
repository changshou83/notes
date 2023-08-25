## 对象方法

```js
console.log()                // 将参数转换为字符串并输出到控制台
console.debug()              // log的别名，不过表现形式不同
console.info()               // log的别名，不过表现形式不同
console.warn()               // log的别名，不过表现形式不同
console.error()              // log的别名，不过表现形式不同

console.assert()             // 通过断言第一个参数，判断是否把剩余参数打印出来
console.clear()              // 清空控制台
console.table()              // 可以生成表列数据输出
console.trace()              // 会打印参数，同时也会打印栈跟踪信息

console.count()              // 打印第一个字符串参数，后面跟着调用次数
console.countReset()         // 重置指定字符的计数

console.group()              // 设置控制台的内部状态，让所有后续的控制台消息相对刚刚打印的消息缩进
console.groupCollapsed()     // 同group，但是信息默认会被折叠
console.groupEnd()           // 停止group函数的缩进

console.time()               // 打印第一个字符串参数，并记录调用时间
console.timeLog()            // 打印距离上次调用的时间
console.timeEnd()            // 打印该参数经过的时间
```

## 格式化输出

```js
console.log(
	"%s, %i, %d, %f, %o, %O, %c hello",
	"string",
	1,
	2.2,
	2.2,
	{ x: 1 },
	{ x: 1 },
	"color: red;"
)
```
