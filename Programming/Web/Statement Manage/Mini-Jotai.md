## 第一版 实现原始值atom

```js
// library code
import { useState, useEffect } from 'react'

const atom = (initialValue) => ({ init: InitialValue }) // 返回一个包含初始值的对象(atom config)

const atomStateMap = new WeakMap() // 状态map，键为atom config，值为atom state
// 使用 atom config 从状态中获取atom state
const getAtomState = (atom) => {
	let atomState = atomStateMap.get(atom)
	if(!atomState) {
		atomState = { value: atom.init, listeners: new Set() } // 一个 state 包含原始值及其订阅者列表
		atomStateMap.set(atom, atomState)
	}
	return atomState
}

const useAtom = (atom) => {
	const [value, setValue] = useState()
	useEffect(() => {
		const callback = () => setValue(atomState.value)
		atomState.listeners.add(callback) // 将每一个使用的地方都当作订阅者
		callback()
		return () => atomState.listeners.delete(callback)
	}, [atomState])

	const setAtom = (nextValue) => {
		atomState.value = nextValue
		atomState.listeners.forEach(l => l()) // 通知所有订阅者状态已变更
	}
	return [value, setAtom]
}
```

## 第二版 实现派生Atom

```js
import { useState, useEffect } from 'react'

const atom = (read, write) => {
	if(typeof read === 'function') {
		return { read, write }
	}
	const config = {
		init: read,
		read: (get) => get(config),
		write: write || (get, set, arg) => {
			if(typeof arg === 'function') {
				set(config, arg(get(config)))
			} else {
				set(config, arg)
			}
		}
	}
	return config
}

const atomStateMap = new WeakMap()
const getAtomState = (atom) => {
	let atomState = atomStateMap.get(atom)
	if(!atomState) {
		atomState = { value: atom.init, listeners: new Set(), dependents: new Set() } // 新增依赖关系列表
		atomStateMap.set(atom, atomState)
	}
	return atomState
}

const readAtom = (atom) => {
	const atomState = getAtomState(atom)
	const get = (a) => {
		// 如果 a 是原始 atom，那么返回它的值
		if(a === atom) {
			return atomState.value
		}
		// 如果 a 是派生 atom，那么就读取其父级的值，并递归地将其添加进其父级及祖先的依赖列表中
		const aState = getAtomState(a)
		aState.dependents.add(atom)
		return readAtom(a)
	}
	const value = atom.read(get) // 这里走 read 的时候就把原始值顺着派生关系的管道一层层处理完了
	atomState.value = value
	return value
}
// 通知所有订阅者以及依赖项
const notify = (atom) => {
	const atomState = getAtomState(atom)
	atomState.dependents.forEach(d => {
		if(d !== atom) notify(d)
	})
	atomState.listeners.forEach(l => l())
}

const writeAtom = (atom, value) => {
	const atomValue = getAtomState(atom)
	const get = a => {
		const aState = getAtomState(a)
		return aState.value
	}
	const set = (a, v) => {
		if(a === atom) {
			atomState.value = v
			notify(atom)
			return
		}
		writeAtom(a, v) // 递归通知
	}
	atom.write(get, set, value)
}

const useAtom = (atom) => {
	const [value, setValue] = useState()
	useEffect(() => {
		const callback = () => setValue(readAtom(atom))
		const atomState = getAtomState(atom)
		atomState.listeners.add(callback)
		callback()
		return () => atomState.listeners.delete(callback)
	}, [atomState])

	const setAtom = (nextValue) => {
		writeAtom(atom, nextValue)
	}
	return [value, setAtom]
}
```

## UseAtom

- [Yet another implementation for Jotai atoms without side effects](https://github.com/dai-shi/use-atom/blob/main/src/Provider.tsx)
	- 核心是 Provider 组件
