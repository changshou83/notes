## 一个简单loader

```js
import { validate } from 'schema-utils'

import schema from './options.json'

export default function(source) {
	const { version, webpack } = this;
	const options = this.getOptions();

	validate(schema, options, 'Loader');

	return `
	/**
	 * Loader API Version: ${version}
	 * Is this in "webpack mode": ${webpack}
	 */
	/**
	 * Original Source From Loader
	 */
	${source}`;
}
```

```json
{
	"type": "object",
	"properties": {
		"name": "boolean"
	},
	"additionalProperties": false
}
```

### 使用

```
const path = require('path');

module.exports = {
  // ...
  module: {
	  rules: [{
		  test: /\.js$/,
		  use: [{
			  loader: path.resolve(__dirname, "../dist/index.js")
		  }]
	  }]
  }
}
```

## 使用上下文接口

- 可以使用上下文接口对构建过程产生一些副作用


## 链式调用


