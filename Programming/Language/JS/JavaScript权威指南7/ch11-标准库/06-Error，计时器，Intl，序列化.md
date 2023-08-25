
## 国际化

- 格式化数值：Intl.NumberFormat
- 格式化时间：Intl.DateTimeFormat
- 比较字符串：Intl.Collator

## 序列化

- 序列化：`JSON.stringify(value[, replacer [, space]])`
	- value：要序列化的值
	- replacer：为函数时，每个属性都会经过该函数的处理；为数组时，将会只序列化数组中的属性名
	- space：为数字时，缩进用的空白字符串的个数；为字符串时，该字符串被视为空格使用
- 反序列化：`JSON.parse(text[, reviver])`
	- text：要被解析的文本
	- reviver：转换器，用来修改解析生成的原始值

## Error

- 使用Error对象的好处是，可以打印出错误信息的栈跟踪信息

## 计时器

- `setTimeout`
	- 即使在严格模式下，函数内的this依然默认指向全局对象，而不是`undefined`
- `setInterval`