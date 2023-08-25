# Recipes

## Large Objects

```js
const initialData = {
  people: [
    { name: 'Luke Skywalker', information: { height: 172 }, siblings: ['John Skywalker', 'Doe Skywalker'] },
    { name: 'C-3PO', information: { height: 167 }, siblings: ['John Doe', 'Doe John'] },
  ],
  films: [
	{ title: 'A New Hope', planets: ['Tatooine', 'Alderaan'] },
	{ title: 'The Empire Strikes Back', planets: ['Hoth'] },
  ],
  info: {
    tags: ['People', 'Films', 'Planets', 'Titles'],
  },
}

const dataAtom = atom(initialData)                                        // 大 atom
const peopleAtom = focusAtom(dataAtom, (optic) => optic.prop('people'))   // 使用 focusAtom 选择大 atom 的某一部分派生新的可读写 atom

const tagsSelector = (s) => s.tags
const Tags = () => {
  const tagsAtom = selectAtom(readOnlyInfoAtom, tagsSelector)             // 使用 selectAtom 选择大 atom 的某一部分，读写属性跟随大 atom
  const tagsAtomsAtom = splitAtom(tagsAtom)                               // 使用 splitAtom 将大 atom 切分成一系列小 atom，读写属性跟随大 atom
  const [tagAtoms] = useAtom(tagsAtomsAtom)
  return (
    <div>
      {tagAtoms.map((tagAtom) => (
        <Tag key={`${tagAtom}`} tagAtom={tagAtom} />
      ))}
    </div>
  )
}
```

- `focusAtom`：选取大atom的某一部分创建一个新的atom，可读写。
- `splitAtom`：将一个大atom切分成一系列的小atom，读写属性跟随大atom。
- `selectAtom`：从一个大atom中选择某一部分创建一个新的atom，读写属性跟随大atom。

## Atom Creators

组合 `atom` 实现各种 `atomWith*`。

```JS
import { WritableAtom, atom } from 'jotai'

function atomWithToggle(initialValue) {
	const anAtom = atom(initialValue, (get, set, nextValue) => {
		const update = nextValue ?? !get(anAtom)
		set(anAtom, update)
	})
	return anAtom
}

// usage
const isActiveAtom = atomWithToggle(true)
const Toggle = () => {
	const [isActive, toggle] = useAtom(isActiveAtom)
	return (
		<>
			<button onCLick={() => toggle()}>
				isActive: {isActive ? 'yes' : 'no'}
			</button>
			<button onClick={() => toggle(true)}>force true</button>
			<button onClick={() => toggle(false)}>force false</button>
		</>
	)
}
```

## Custom useAtom hooks

组合 `useAtom` 和其他函数制作自定义 `useAtom` 钩子函数。注意要保持引用相等，即需要记忆化，否则会导致 useAtom 无限循环。

```js
const useSelectAtom = (anAtom, keyFn) => useAtomValue(selectAtom(anAtom, keyFn))
useSelectAtom(
	useMemo(() => atom(initValue), [initValue]),
	useCallback((state) => state.prop, [])
)

const useFreezeAtom = anAtom => useAtom(freezeAtom(anAtom))
const useSplitAtom = anAtom => useAtom(splitAtom(anAtom))
```
