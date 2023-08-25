- 异步函数的类型
	- 异步I/O函数：例如`Ajax`
	- 异步计时函数：`setTimeout`，`setInterval`，`requestAnimationFrame`，`process.nextTick`
- 异步函数的错误处理
	- `try/catch`捕获不到异步错误
	- 如果遇上意料之外的错误
		- 考虑应该如何处理错误
		- 考虑是否让应用直接崩溃(不处理错误)，或者停止工作(throw错误)
- 嵌套式回调的解嵌套
	- 避免两层以上的函数嵌套
	- 在异步函数之外存储异步结果

## 示例代码

### 嵌套式回调的解嵌套

```js
function checkPassword(username, passwordGuess, cb) {
	const queryStr = 'SELECT * FROM user WHERE username = ?'
	db.query(queryStr, username, function(err, result) {
		if(err) throw err;
		hash(passwordGuess, function(passwordGuessHash) {
			cb(passwordGuessHash === result['password_hash']);
		})
	})
}
// => 优化结构后
function checkPassword(username, passwordGuess, cb) {
	let passwordHash;
	const queryStr = 'SELECT * FROM user WHERE username = ?'
	db.query(queryStr, username, queryFn)

	function queryFn(err, result) {
		if(err) throw err;
		passwordHash = result['password_hash'];
		hash(passwordGuess, hashFn);
	}
	function hashFn(passwordGuessHash) {
		cb(passwordHash === passwordGuessHash);
	}
}
```