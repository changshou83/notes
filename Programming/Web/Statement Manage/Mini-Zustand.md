## 第一版：在多组件间共享状态

```js
// library code
const createStore = (initialState) => {
	let state = initialState;
	const getState = () => state;
	const setState = (nextState) => {
		state = nextState;
		listeners.forEach(listener => listener());
	};
	const listeners = new Set();
	const subscribe = (listener) => {
		listeners.add(listener);
		return () => listeners.delete(listener);
	};
	return { getState, setState, subscribe };
}

// application code
const store = createStore({ count: 0 });
const Counter1 = () => {
	const [state, setState] = useState(state.getState());
	useEffect(() => {
		const callback = () => setState(state.getState());
		const unsubscribe = state.subscribe(callback);
		return ubsubscribe;
	}, []);
	const inc = () => {
		const nextState = { count: state.getState().count + 1 };
		store.setState(nextState);
	};
	return (
		<>
			{state.count} <button onCLick={inc}>+1</button>
		</>
	);
};
const Counter2 = () => {
	const [state, setState] = useState(state.getState());
	useEffect(() => {
		const callback = () => setState(state.getState());
		const unsubscribe = state.subscribe(callback);
		return ubsubscribe;
	}, []);
	const inc = () => {
		const nextState = { count: state.getState().count + 1 };
		store.setState(nextState);
	};
	return (
		<>
			{state.count} <button onCLick={inc}>+1</button>
		</>
	);
};
```

## 第二版：将使用状态的逻辑分离到自定义Hook中

```js
// library code
const createStore = (initialState) => {
	let state = initialState;
	const getState = () => state;
	const setState = (nextState) => {
		state = nextState;
		listeners.forEach(listener => listener());
	};
	const listeners = new Set();
	const subscribe = (listener) => {
		listeners.add(listener);
		return () => listeners.delete(listener);
	};
	return { getState, setState, subscribe };
}

const useStore = (store) => {
	const [state, setState] = useState(store.getState());
	useEffect(() => {
		const callback = () => setState(state.getState());
		const unsubscribe = state.subscribe(callback);
		callback();
		return ubsubscribe;
	}, [store]);
	return [store, store.setState];
};

// application code
const store = createStore({ count: 0 });
const Counter1 = () => {
	const [state, setState] = useStore(store);
	const inc = () => {
		const nextState = { count: state.getState().count + 1 };
		setState(nextState);
	};
	return (
		<>
			{state.count} <button onCLick={inc}>+1</button>
		</>
	);
};
const Counter2 = () => {
	const [state, setState] = useStore(store);
	const inc = () => {
		const nextState = { count: state.getState().count + 1 };
		setState(nextState);
	};
	return (
		<>
			{state.count} <button onCLick={inc}>+1</button>
		</>
	);
};
```

## 第三版：实现状态选择器以避免不必要的重新渲染

```js
// library code
const createStore = (initialState) => {
	let state = initialState;
	const getState = () => state;
	const setState = (nextState) => {
		state =
	      typeof nextState === 'function'
	        ? nextState(state)
	        : nextState;
		listeners.forEach(listener => listener());
	};
	const listeners = new Set();
	const subscribe = (listener) => {
		listeners.add(listener);
		return () => listeners.delete(listener);
	};
	return { getState, setState, subscribe };
}
const identity = x => x;
const useStore = (store, selector = identity) => {
	const [state, setState] = useState(selector(store.getState()));
	useEffect(() => {
		const callback = () => setState(selector(store.getState()));
		const unsubscribe = state.subscribe(callback);
		callback();
		return ubsubscribe;
	}, [store, selector]);
	return [store, store.setState];
};

// application code
const store = createStore({ count1: 0, count2: 0 });
const Counter1 = () => {
	const [count1, setState] = useStore(store, useCallback((state) => state.count1, []));
	const inc = () => setState((prev) => ({ ...prev, count1: prev.count1 + 1 }));
	return (
		<>
			{count1} <button onCLick={inc}>+1</button>
		</>
	);
};
const Counter2 = () => {
	const [count2, setState] = useStore(store, useCallback((state) => state.count2, []));
	const inc = () => setState((prev) => ({ ...prev, count2: prev.count2 + 1 }));
	return (
		<>
			{count2} <button onCLick={inc}>+1</button>
		</>
	);
};
```

## 第四版：将改变状态的动作添加到仓库中

```js
// library code
const createStore = (createState) => {
	const getState = () => state;
	const setState = (nextState) => {
		state =
	      typeof nextState === 'function'
	        ? nextState(state)
	        : nextState;
		listeners.forEach(listener => listener());
	};
	let state = createState(setState);
	const listeners = new Set();
	const subscribe = (listener) => {
		listeners.add(listener);
		return () => listeners.delete(listener);
	};
	return { getState, setState, subscribe };
}
const identity = x => x;
const useStore = (store, selector = identity) => {
	const [state, setState] = useState(selector(store.getState()));
	useEffect(() => {
		const callback = () => setState(selector(store.getState()));
		const unsubscribe = state.subscribe(callback);
		callback();
		return ubsubscribe;
	}, [store, selector]);
	return store;
};

// application code
const store = createStore((setState) => ({
	count1: 0,
	count2: 0,
	inc1: () => { setState((prev) => ({ ...prev, count1: prev.count1 + 1 })) },
	inc2: () => { setState((prev) => ({ ...prev, count2: prev.count2 + 1 })) },
}));
const Counter1 = () => {
	const count1 = useStore(store, useCallback((state) => state.count1, []));
	const inc1 = useStore(store, useCallback((state) => state.inc1, []));
	return (
		<>
			{count1} <button onCLick={inc1}>+1</button>
		</>
	);
};
const Counter2 = () => {
	const count2 = useStore(store, useCallback((state) => state.count2, []));
	const inc2 = useStore(store, useCallback((state) => state.inc2, []));
	return (
		<>
			{count2} <button onCLick={inc2}>+1</button>
		</>
	);
};
```
