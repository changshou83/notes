
## 选择DOM元素

- 通过CSS选择符：`querySelector()`，`querySelectorAll`

## 文档结构与遍历

- Element(元素节点)对象属性：`parentNode`,`children`,`childElementCount`,`firstElementChild`,`lastElementChild`,`previousElementSibling`,`nextElementSibling`
- Node(节点)对象属性：`parentNode`,`childNodes`.`firstChild`,`lastChild`,`previousSibling`,`nextSibling`,`nodeType`,`nodeValue`,`nodeName`

```js
function textContent(el) {
	let str = '';
	for(let child = el.firstChild; child != null; child = child.nextSibling) {
		const type = child.nodeType;
		if(type === 3) str += child.nodeValue;
		else if(type === 1) str += child.textContent;
	}
	return str;
}
```

## 属性

- 元素属性
	- 该HTML元素的属性
	- 操作属性：`el.getAttribute()`,`el.setAttribute()`,`el.hasAttribute()`,`el.removeAttribute()`
- class：通过`el.classList`操作
- dataset：用户自定义属性，`data-x`可以通过`el.dataset.x`访问

## 元素内容

- HTML内容：`innerHTML`
- 文本内容：`textContent`

## 操作节点

- 创建
	- 元素节点：`document.createElement()`
	- 文本节点：`document.createTextNode()`
- 插入
	- 元素节点：`append()`,`prepend()`
	- 文本节点：`after()`,`before()`
- 删除：`replaceWith()`,`remove()`
- 老方法：`appendChild()`,`insertBefore()`,`replaceChild()`,`removeChild()`

## 示例

```js
document.addEventListener('DOMContentLoaded', () => {
	// 获取 toc 元素
	let toc = document.querySelector('#TOC')
	if(!toc) {
		toc = document.createElement('div');
		toc.id = 'TOC';
		document.body.body.prepend(toc);
	}
	// 找到所有h2-h6元素
	const headings = document.querySelectorAll('h1,h2,h3,h4,h5,h6');
	const sectionNumbers = [0,0,0,0,0]; // 跟踪节号
	for(let heading of headings) {
		// 如果标题位于TOC元素中则跳过
		if(heading.parentNode === toc) continue;
		// 确定标题级别，h2 为第 1 级标题
		const level = parseInt(heading.tagName.charAt(1)) - 1;
		sectionNumbers[level-1]++;
		for(let i = level; i < sectionNumbers.length; i++) {
			sectionNumbers[i] = 0; // 把所有低级编号重置为 0
		}
		// 创建节号
		const sectionNumber = sectionNumbers.slice(0, level).join('.');
		// 把节号添加到节标题中
		const span = document.createElement('span');
		span.className = 'TOCSectNum'; // 可用于修改节号样式
		span.textContent = sectionNumber;
		heading.prepend(span);
		// 给标题套一层链接，以便可以链接到它
		const anchor = document.createElement('a');
		anchor.name = `TOC${sectionNumber}`;
		heading.before(anchor);
		anchor.append(heading);
		// 创建这一节的链接
		const link = document.createElement('a');
		link.href = `#${fragmentName}`;
		link.innerHTML = heading.innerHTML;
		// 把链接放到一个div中，方便添加样式
		const entry = document.createElement('div');
		entry.classList.add('TOCEntry', `TOCLevel${level}`);
		entry,append(link);
		toc.append(entry); // 把div加入到容器中
	}
})
```
