
## 使用Web组件

```html
<style>
// 在 search-box 标签被解析但组件未被加载时隐藏
search-box:not(:defined) {
	opacity: 0;
	display: inline-block;
	width: 300px;
	height: 50px;
}
</style>
<search-box>
	<img src="" slot="left" />
	<img src="" slot="right" />
</search-box>
```

## HTML模板

- 使用template标签存储组件标签和样式
- 然后克隆template标签插入到文档中

## 自定义元素

- 可以将一个HTML标签与一个JavaScript类关联起来
- 创建自定义元素`customElement.define(name, class)`
- 自定义类应该扩展`HTMLElement`，还需要调用`super()`方法
- 生命周期
	- 元素被插入到文档时，`connectedCallback()`
	- 被从文档中移除时，`disconnectedCallback()`
- 属性
	- 静态`observedAttributes`属性，值为一个数组，含义为被观察的属性，如果有属性被改动，浏览器就会调用`attributeChangedCallback()`

## 影子`DOM`

- 影子`DOM`允许把一个“影子根节点”附加给一个元素(影子宿主)
- 影子根节点是另一个更私密的后代元素树的根，可以当做一个迷你文档
- 为什么是影子？因为是在常规`DOM`元素的内部，而不在常规`DOM`树中
- 事件
	- 状态变化事件发生在影子`DOM`内部，不会传到外部
	- 用户事件会向上冒泡，在影子`DOM`内部是发生在具体元素，在影子`DOM`外部是发生在宿主元素
- 与子元素
	- 如果影子`DOM`中有插槽，那就渲染到插槽内，如果没有就忽略
	- 插槽中的内容会作为默认子元素渲染
	- 注意子元素不属于影子`DOM`
	- 插槽可以命名
- API
	- `attachShadow()`：将一个`DOM`元素转换为影子`DOM`的宿主元素
	- `shadowRoot`：指向影子根节点对象

## 示例

```js
class SearchBox extends HTMLElement {
	constructor() {
		super(); // 必须先调用

		// 创建 shadow DOM
		this.attachShadow({ mode: 'open' });
		// 克隆模板，并添加到影子根节点
		this.shadowRoot.append(Search.template.content.cloneNode(true));
		
		this.input = this.shadowRoot.querySelector('#input');
		const leftSlot = this.shadowRoot.querySelector('slot[name="left"]');
		const rightSlot = this.shadowRoot.querySelector('slot[name="right"]');
		
		this.input.onfocus = () => { this.setAttribute('focused', "") }
		this.input.onblur = () => { this.setAttribute('focused') }

		// 点击放大镜，触发 search 事件
		leftSlot.onclick = this.input.onchange = (evt) => {
			evt.stop.Propagation();
			
			if(this.disabled) return;
			
			this.dispatchEvent(new CustomEvent('search', {
				detail: this.input.value
			}))
		}
		// 点击 X， 触发 clear 事件
		leftSlot.onclick = (evt) => {
			evt.stopPropagation();
			
			if(this.disabled) return;
			
			const e = new CustomEvent('clear', { cancelabled: true });
			this.dispatchEvent(e);
			
			if(!e.defaultPrevented) { this.input.value = ""; }
		}
	}
	// 在修改属性时，浏览器自动调用
	attributeChangedCallback(name, oldV, newV) {
		switch(name) {
			case "disabled": this.input.disabled = newV !== null;break;
			case "placeholder": this.input.placeholder = newV;break;
			case "size": this.input.size = newV;break;
			case "value": this.input.value = newV;break;
			default: break;
		}
	}
	
	get placeholder() { return this.getAttribute("placeholder") }
	get size() { return this.getAttribute("size") }
	get value() { return this.getAttribute("value") }
	get disabled() { return this.hasAttribute("disabled") }
	get hidden() { return this.hasAttribute("hidden") }
	
	set placeholder(val) { this.setAttribute("placeholder", val) }
	set size(val) { this.setAttribute("size", val) }
	set value(text) { this.setAttribute("value", text) }
	set disabled(val) {
		if(val) this.setAttribute("disabled","")
		else this.removeAttribute("disabled")
	}
	set hidden(val) {
		if(val) this.setAttribute("hidden","")
		else this.removeAttribute("hidden")
	}
	
	static observedAttributes = ['disabled', 'placeholder', 'size', 'value']
	static template = document.createElement('template')
}

SearchBox.template.innerHTML = `
<style>
:host {
	display: inline-block;
	border: solid black 1px;
	border-radius: 5px;
	padding: 4px 6px;
}
:host([hidden]) { display: none; }
:host([disabled]) { opacity: 0.5; }
:host([focused]) { box-shadow: 0 0 2px 2px #6AE; }

input {
	border-width: 0;
	outline: none;
	font: inherit;
	background: inherit;
}
slot {
	cursor: default;
	user-select: none;
}
</style>
<div>
	<slot name="left">\u{1f50d}</slot>
	<input type="text" id="input" />
	<slot name="right">\u{2573}</slot>
</div>
`

customElements.define('search-box', SearchBox);
```
