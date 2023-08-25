
- 就是`addEventListener`

```js
const PubSub = {
	handlers: new Map(),
	on(type, handler, opts = {}) {
		const { once = false } = opts;
		if(!this.handlers.has(type)) {
			this.handlers.set(type, new Set());
		}
		const callbackSet = this.handlers.get(type)
		callbackSet.add(() => {
			handler();
			if(once) this.remove(type, handler);
		});
		return this;
	},
	emit(type) {
		const handlers = this.handlers.get(type)
		if(handlers) {
			for(const handler of handlers) {
				handler();
			}
		}
		return this;
	},
	remove(type, target) {
		const handlers = this.handlers.get(type)
		if(handlers) {
			for(const handler of handlers) {
				if(handler === target) {
					handlers.delete(handler)
					return;
				}
			}
		}
	},
}
```
