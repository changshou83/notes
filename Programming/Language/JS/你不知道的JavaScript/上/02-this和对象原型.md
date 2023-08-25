
## this

- 关于`this`
	- 函数作用域在运行时绑定的特殊参数，同样的还有`arguments`
	- `this`指向什么取决于函数被如何调用
- `this`全面解析
	- 优先级：`new` > `bind,apply,call` > `obj.` > `window/undefined`
	- 普通函数(`bind,apply,call`,`window/undefined`)
	- 对象方法(`bind,apply,call`,`obj.`)
	- 构造器(`new`)
- 注意事项：箭头函数不绑定`this`，因此它的`this`是父函数作用域的this

## 对象原型

- 对象
	- 特点
		- 除基础类型以外的值
		- 键值存储
		- 键永远是字符串类型或者Symbol类型
		- 属性名可计算
	- 属性描述符：`enumerable`,`value`,`configurable`,`writable`
	- 不变性
		- 不可扩展，对象不可添加，`Object.preventExtensible()`
		- 密封，属性描述符(除`value`)不可修改，对象不可添加，`Object.seal()`
		- 冷冻，属性描述符不可修改，对象不可添加，`Object.freeze()`
	- 访问器属性：`getter/setter`
	- 遍历
		- `Object.keys()`,`Object.values()`,`Object.entries()`返回可迭代对象(有返回迭代器的`[Symbol.iterator]`方法)
		- `for/in`,`for/of`
- 原型
	- 使用构造器从原型对象生成新对象
	- 属性查找会沿着原型链进行，因此原型本质上表示对象间的关联关系
	- 属性及方法
		- 访问原型对象`{}.constructor.prototype`
		- 从原型创建对象`Object.create()`
		- 访问原型对象的原型`Object.getPrototypeOf({}.constructor.prototype)` 
		- 获取对象自有属性`Object.getOwnPropertyNames()`
	- 继承
		- `A = Object.create(null)`
		- `A.constructor = A`
	- 注意事项：`Object.getPrototypeOf(Function) === Object.getPrototypeOf(Object)`为true
