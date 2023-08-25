# Core

只公开了主包中的部分导出，因此只有2kb，分为四大类：atom,useAtom,Store,Provider。

## `atom`
	
 一个函数，用于创建原子状态配置。
 
### 分类

- `primitive atom`：`const numberOfAtom = atom(10)`
- `derived atom`
	- `read-only`
		- `atom(read)`
		- 例如：`atom((get) => get(numberOfAtom) * 2)`
	- `write-only`
		- `atom(null, write)`
		- 例如：`atom(null, (get, set, update) => set(numberOfAtom, get(numberOfAtom) + update.cnt))`
	- `read-write`
		- `atom(read, write)`
		- 例如：`atom((get) => get(numberOfAtom) * 2, (get, set, newNumber) => set(numberOfAtom, newNumber / 2))`

### 注意事项

- 动态创建 `atom` 时要使用`useMemo`或者`useRef`，例如：`const valueAtom = useMemo(() => atom({ value }, [value])`，否则会导致`useAtom`无限循环。

### 属性

- `debugLabel`：在调试器中的标签
- `onMount`：一个接收`setAtom`函数并返回onUnmount函数的函数。在首次使用atom时调用，onUnmount在不再使用时调用。

### 高级API

在v2中，read函数拥有了第二个参数`options`

- `options.signal`：是一个AbortController，用于终止异步函数，其在调用read函数前触发。

## `useAtom`

一个Hook，用于读取状态中的原子值，并使用它创建一个原子值。状态是原子值与原子状态配置之间的弱引用。

最初，没有值与atom相关联，只有在使用useAtom时，atom的初始值才会被存储在状态中。当不再使用atom时，状态中的atom将会被回收。

### 使用

```js
const stableAtom = atom(0);
const [value, setValue] = useAtom(stableAtom);
```

setValue只接受一个参数，该参数被传递给write函数的第三个参数。

### 其他Hook

```js
const countAtom = atom(0)

const Counter = () => {
	const setCount = useSetAtom()
	const count = useAtomValue()
	
	return (
		<>
			<div>count: {count}</div>
			<button onCLick={() => setCount(count + 1)}>+1</button>
		</>
	)
}
```

- useAtomValue：只访问只读原子。
- useSetAtom：在不读取原子值的情况下更新它。好处是可以避免`const [, setValue] = useAtom`导致的不必要的重新渲染。

## `Provider`

`Provider` 组件为组件子树提供状态，就像 `React Context` 一样。如果在没有 `Provider` 的组件树中使用 `atom`，Jotai将使用默认状态。

使用 `Provider` 的好处：
1. 初始化原子初始值。
2. 在重新挂载时清除所有原子状态。
3. 为不同子树提供不同状态。

### 使用

```js
const store = createStore()

const Root = () => {
	<Provider store={store}>
		<App />
	</Provider>
}

const Component = () => {
	const store = useStore()
}
```

## `Store`

`Store` 可用于初始化 `Provider` 的状态。

### Methods

```js
const store = createStore()

const countAtom = atom(0)
store.set(countAtom, 1)
const unsub = store.sub(countAtom, () => {
	console.log('countAtom value is changed to', store.get(countAtom))
})
```

`Store` 有三种方法：
1. `get`：获取原子值
2. `set`：设置原子值
3. `sub`：订阅原子更改

### Hooks

- `createStore`：创建一个空的状态仓库
- `getDefaultStore`：获取默认的状态仓库
- `useStore`：使用状态仓库
