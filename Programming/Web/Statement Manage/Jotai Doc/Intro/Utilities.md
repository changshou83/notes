# Utilities

使用 `jotai/utils` 提供了一些实用功能。
分为五大类：Storage，SSR，Async，Resettable，Family。

## Storage

```js
import { useAtom } from 'jotai'
import { atomWithStorage } from 'jotai/utils'

const darkModeAtom = atomWithStorage('darkMode', false)

const Page = () => {
	const [darkMode, setDarkMode] = useAtom(darkModeAtom)

	return (
		<>
			<h1>Welcome to {darkMode ? 'dark' : 'light'} mode!</h1>
			<button onClick={() => setDarkMode(!darkMode)}>toggle theme</button>
		</>
	)
}
```

使用 `atomWithStorage` 创建一个atom，并将其值持久化在 `WebStorage` 或者 `AsyncStorage` 中。

### 参数

`const atomConfig = atomWithStorage(key, initialValue[, storage])`

- `key`(required)：存储在storage中的键。
- `initialValue`(required)：原子初始值。
- `storage`(optional)：存储位置，默认 `localstorage`。值为一个对象。
	- `getItem(key, initialValue)`(required)：默认`JSON.parse()`反序列化。
	- `setItem(key, value)`(required)：默认`JSON.stringify()`序列化。
	- `removeItem(key)`(required)
	- `subscribe(key, callback, initialValue)`(optional)：订阅外部存储更新的方法，默认订阅`storage`事件以进行跨标签页同步。

### 其他

- SSR：因为serve上没有webstorage，因此可以将用到的状态包括在 `<ClientOnly>` 标签中。
- 删除项：`atomWithStorage` 原子在写入时，接收 `RESET` 符号，如果传入此符号，则初始化为初始值。
- 验证存储值：此功能需要自定义存储实现。

## SSR

```js
import { atom useAtom } from 'jotai'
import { useHydrateAtoms } from 'jotai/utils'

const countAtom = atom(0)
const CounterPage = ({ countFromServer }) => {
	useHydrateAtoms([ [countAtom, countFromServer] ])
	const [count] = useAtom(countAtom); // count would be the value of `countFromServer`, not 0
}
```

使用 `useHydrateAtoms` 将从服务器获取的值作为初始值注入到原子状态中。

### 参数

`useHydrateAtoms(atomValueMap[, options])`

- `atomValueMap`(required)：一个map或元组，结构为`[atom, value]`。
- `options`(optional)：配置对象
	- `store`：指定要注入的状态仓库。
	- `dangerouslyForceHydrate`：每个仓库中的状态最多被注入一次，如果想要在 `value` 更新时也重新注入 `atom`，那么就设置此项为 `true`。

## Async

### loadable

```js
import { loadable } from 'jotai/utils'

const asyncAtom = atom(async (get) => await fetch().then(data => data.json()).then(data => `${data} + ${get(standAtom)}`))
const loadableAtom = loadable(asyncAtom)
const Component = () => {
	const [value] = useAtom(loadableAtom)
	if(value.state === 'hasError') return <Text>{value.error}</Text>
	if(value.state === 'loading') return <Text>Loading...</Text>
	return <Text>Value: {value.data}</Text>
}
```

对异步原子进行更细粒度的控制。

### 参数

`const loadableAtom = loadable(atom)`

- atom：传入一个要控制的atom

### 返回值

`const [value] = useAtom(loadableAtom)`

- value
	- `value.state`：`'loading' | 'hasData' | 'hasError'`，表示异步原子执行状态
	- `value.data`：返回的数据
	- `value.error`：错误信息

### atomWithObservable

```js
import { useAtom } from 'jotai'
import { atomWithObservable } from 'jotai/utils'
import { interval } from 'rxjs'
import { map } from 'rxjs/operators'

const counterSubject = interval(1000).pipe(map(i => `#${i}`))
const counterAtom = atomWithObservable(() => counterSubject, { initialValue: 10 })

const Counter = () => {
	const [counter] = useAtom(counterAtom)
	return <div>count: {counter}</div>
}
```

## Resettable Atom

```js
import { atom, useSetAtom } from 'jotai'
import { atomWithReset, atomWithDefault, RESET, useResetAtom } from 'jotai/utils'

const dollarsAtom = atomWithReset(0)
const centsAtom = atom(
	(get) => get(dollarsAtom) * 100,
	(get, set, newValue) => set(dollarsAtom, newValue === RESET ? newValue : newValue / 100)
)

const ResetExample = () => {
	const setDollars = useResetAtom(dollarsAtom) // 重置可重置原子
	const resetCents = useSetAtom(centsAtom) // 重置可写原子
	return (
		<>
			<button onClick={() => setDollars(RESET)}>Reset dollars</button>
			<button onClick={resetCents}>Reset cents</button>
		</>
	)
}
```

- atomWithReset：创建一个可以被useResetAtom重置为初始值的原子状态，其值为原始值。
- atomWithDefault：创建一个可以被useResetAtom重置为初始值的原子状态，其值为函数。
- RESET：可以被atomWithReset或atomWithDefault创建的可重置的atom所接收的特殊值，可写atom也可以接收。
- useResetAtom：将可重置原子重置为其初始值。

## Family

atom的工厂函数

```ts
import React from "react";
import { atom, Provider, useAtom, useSetAtom } from "jotai";
import { atomFamily } from "jotai/utils";

const todoAtomFamily = atomFamily(
	({ title }: Todo) => atom({ title: title || "No Title", completed: false }),
	(a, b) => a.id === b.id
);
  
const filterAtom = atom<"all" | "completed" | "incompleted">("all");
const todosAtom = atom<string[]>([]);
const filteredAtom = atom((get) => {
	const todos = get(todosAtom);
	const filter = get(filterAtom);
	if (filter === "all") return todos;
	if (filter === "completed") {
		return todos.filter((id) => get(todoAtomFamily({ id })).completed);
	}
	return todos.filter((id) => !get(todoAtomFamily({ id })).completed);
});
```
