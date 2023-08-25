# Guide

## 组合原子

### 使用两个原子实现 atomWithDefault

```js
const rawNumberAtom = atom(10.1)
const roundNumberAtom = atom(get => Math.round(get(rawNumberAtom)))
const numberAtom = atomWithDefault((get) => get(roundNumberAtom))

const overwrittenAtom = atom(null)
const numberAtom = atom(
	(get) => get(overwrittenAtom) ?? get(roundNumberAtom),
	(get, set, newValue) => {
		const nextValue = typeof newValue === 'function' ? newValue(get(numberAtom)) : newValue
		set(overwrittenAtom, nextValue)
	}
)
```

### 使用两个原子实现atomWithStorage

```js
const baseAtom = atom(localstorage.getItem('key') || '')
const persistedAtom = atom(
	(get) => get(baseAtom),
	(get, set, newValue) => {
		const nextValue = typeof newValue === 'function' ? newValue(get(numberAtom)) : newValue
		set(baseAtom, nextValue)
		localstorage.setItem('key', nextValue)
	}
)
```

## 在渲染时初始化状态

场景：创建一个使用Atom的可重用组件。

假设一个组件的内部有两个子组件，而这两个子组件使用同一个Atom，如果要使这个组件变为可重用组件，那么就要为其注入不同的初始值。
```js
const textAtom = atom('')
// 使用同一Atom的两个组件
const PrettyText = () => {
  const [text] = useAtom(textAtom)
  return (
    <>
      <text style={{ color: 'blue' }}>
        {text}
      </text>
    </>
  )
}
const UpdateTextInput = () => {
  const [text, setText] = useAtom(textAtom)
  const handleInputChange = (e) => setText(e.target.value)
  return (
    <>
      <input onChange={handleInputChange} value={text} />
    </>
  )
}

// 注入初始值的 wrapper 组件
const HydrateAtoms = ({ initialValue, children }) => {
	useHydrateAtoms(initialValue)
	return children
}

// 可重用组件
const TextDisplay = ({ initialTextValue }) => {
	return (
		<Provider>
			<HydrateAtoms initialValue={[[textAtom, initialTextValue]]}>
				<PrettyText />
				<br />
				<UpdateTextInput />
			</HydrateAtoms>
		</Provider>
	)
}

// usage
export default function App() {
    return (
	    <div className="App">
		    <TextDisplay initialTextValue="initial text value 1" />
			<TextDisplay initialTextValue="initial text value 2" />
		</div>
	)
}
```
